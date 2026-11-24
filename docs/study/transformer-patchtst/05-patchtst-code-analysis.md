---
title: "PatchTST 공식 코드 분석: 실행 진입점에서 모델 구조까지"
date: 2023-11-24
last_modified_at: 2023-11-25
category: study
tags: [PatchTST, 시계열 예측, PyTorch, 코드 분석, RevIN, Patching]
---

# PatchTST 공식 코드 분석: 실행 진입점에서 모델 구조까지

작성일 2023-11-24 · 최종 수정일 2023-11-25
{ .post-date }

[PatchTST 성능 개선 프로젝트](../../projects/ai-course/03-patchtst.md)를 시작하면서 PatchTST의 공식 구현을 실행 진입점부터 따라가며 읽은 기록이다. PyTorch로 짠 연구 코드를 처음부터 끝까지 읽어 본 것이 처음이라, 모델 구조뿐 아니라 인자를 받는 방법이나 데이터를 불러오는 방법 같은 기본적인 부분도 함께 정리했다. 읽은 코드는 공식 저장소의 `PatchTST_supervised` 폴더이다.

모델 안에서 데이터가 실제로 흘러가는 과정은 [PatchTST의 forward 과정 따라가기](09-patchtst-forward-pass.md)에, 논문 내용은 [PatchTST 논문 요약](10-patchtst-paper-summary.md)에 따로 정리했다.

## 전체 흐름

코드는 다음 순서로 호출된다.

| 순서 | 파일 | 역할 |
|---|---|---|
| 1 | `run_longExp.py` | 실행 진입점. 인자를 읽고 실험을 반복한다 |
| 2 | `exp/exp_main.py`, `exp/exp_basic.py` | 실험 클래스. 데이터 준비, 학습, 테스트를 맡는다 |
| 3 | `data_provider/data_factory.py`, `data_loader.py` | 데이터셋과 DataLoader를 만든다 |
| 4 | `models/PatchTST.py` | 모델 클래스. 분해 옵션에 따라 backbone을 만든다 |
| 5 | `layers/PatchTST_backbone.py` | RevIN, patching, Transformer encoder, head |

## 1. run_longExp.py: 실행 진입점

### 인자 읽기

모델을 구성하는 데 필요한 값은 모두 명령행 인자로 받는다. 이때 쓰는 것이 `argparse` 모듈이다. 파이썬 스크립트가 옵션에 따라 다르게 동작하려면 명령행에서 인자를 넘겨받아 해석해야 하는데, `argparse`가 그 해석을 맡는다. 사용 순서는 다음과 같다.

먼저 `ArgumentParser` 객체를 만든다. 이 객체가 인자를 해석하는 데 필요한 정보를 가지고 있다. `description`은 프로그램 설명이고 생략할 수 있다.

```python
parser = argparse.ArgumentParser(description='Autoformer & Transformer family for Time Series Forecasting')
```

다음으로 `add_argument`로 받을 인자를 하나씩 정의한다.

```python
parser.add_argument('--random_seed', type=int, default=2021, help='random seed')
parser.add_argument('--model', type=str, required=True, default='Autoformer', help='model name')
```

- `type`: 인자의 자료형
- `default`: 인자를 주지 않았을 때 쓰는 값
- `help`: 인자에 대한 설명
- `required=True`: 반드시 줘야 하는 인자

마지막으로 `parse_args()`를 호출하면 해석된 값이 담긴 객체가 돌아오고, `args.random_seed`, `args.model`처럼 꺼내 쓴다.

```python
args = parser.parse_args()
```

`run_longExp.py`가 받는 인자는 주석으로 묶여 있다. random seed, 기본 설정, 데이터 로더, 예측 task, PatchTST 관련 설정, Transformer 계열 공통 설정, 최적화, GPU 순서이다. 결국 이 부분은 모델 구조와 학습에 필요한 설정값 전부를 받는 곳이다.

대부분의 인자에는 기본값이 있지만 `--is_training`, `--model_id`, `--model`, `--data` 네 개는 반드시 줘야 한다. 실제로는 인자를 손으로 치지 않고, 저장소의 `scripts/PatchTST/` 아래에 있는 데이터셋별 셸 스크립트가 값을 채워서 실행한다. ETTh1용 스크립트는 예측 길이 96, 192, 336, 720 네 가지에 대해 학습과 테스트를 차례로 돌린다.

### seed와 GPU 설정

난수를 쓰는 모듈 세 곳(`random`, `torch`, `numpy`)에 같은 seed를 지정해 실험을 재현할 수 있게 한다.

```python
fix_seed = args.random_seed
random.seed(fix_seed)
torch.manual_seed(fix_seed)
np.random.seed(fix_seed)
```

GPU는 사용할 수 있는지 확인한 뒤 쓰고, 여러 개를 쓰는 옵션이 켜져 있으면 장치 번호 목록을 만들어 둔다.

### 실험 반복

실제로 실험을 돌리는 부분이다. `is_training`이 참이면 `itr` 횟수만큼 학습과 테스트를 반복하고, 거짓이면 테스트만 한다.

```python
Exp = Exp_Main

if args.is_training:
    for ii in range(args.itr):
        setting = '...'            # 실험 설정을 이어 붙인 이름
        exp = Exp(args)
        exp.train(setting)
        exp.test(setting)
        torch.cuda.empty_cache()
else:
    exp = Exp(args)
    exp.test(setting, test=1)
    torch.cuda.empty_cache()
```

`setting`은 모델 이름, 데이터, 입력 길이, 예측 길이, 모델 차원 같은 설정값을 이어 붙인 문자열이고, checkpoint와 결과를 저장하는 폴더 이름으로 쓰인다. 한 번의 실험이 끝나면 `torch.cuda.empty_cache()`로 GPU cache를 비운다.

설정이 끝나면 `Exp_Main`의 `train()`으로 넘어간다.

## 2. Exp_Main과 Exp_Basic: 실험 클래스

`Exp_Main`은 `Exp_Basic`을 상속받는다. `Exp_Basic`의 `__init__`은 두 가지 일을 한다.

1. 인자 설정에 따라 CUDA를 쓸지 CPU를 쓸지 정하고, 학습을 실행할 장치 객체를 가져온다. `torch.device('cuda:1')`처럼 번호를 지정해 사용할 GPU를 고를 수 있다.
2. 모델을 만들어 그 장치로 옮긴다. 모델이 GPU 메모리에 올라가야 GPU에서 연산할 수 있다.

```python
self.device = self._acquire_device()
self.model = self._build_model().to(self.device)
```

`Exp_Main`이 `Exp_Basic`을 상속받으므로 `Exp_Main` 안에서도 `self.device`와 `self.model`에 접근할 수 있다. `Exp_Main`의 `_build_model()`은 `PatchTST` 클래스로부터 모델을 만든다.

처음 읽을 때는 `Exp_Basic`이 자기에게 구현되어 있지 않은 `_build_model()`을 어떻게 호출하는지가 이해되지 않았다. 답은 아래 "남았던 질문과 답"에 적었다.

## 3. 데이터 불러오기

`train()`은 가장 먼저 학습, 검증, 테스트용 데이터를 가져온다.

```python
train_data, train_loader = self._get_data(flag='train')
vali_data, vali_loader = self._get_data(flag='val')
test_data, test_loader = self._get_data(flag='test')
```

### data_provider

`data_factory.py`의 `data_provider(args, flag)`는 `flag`(train, val, test, pred)에 맞는 데이터셋과 DataLoader를 만들어 돌려준다. 어떤 데이터셋 클래스를 쓸지는 `--data` 인자로 정한다.

| `--data` | 클래스 |
|---|---|
| `ETTh1`, `ETTh2` | `Dataset_ETT_hour` |
| `ETTm1`, `ETTm2` | `Dataset_ETT_minute` |
| `custom` | `Dataset_Custom` |

### DataLoader

DataLoader는 데이터를 여러 개의 mini-batch로 만들어 준다. 데이터셋 전체를 한 번에 GPU 메모리에 올릴 수 없으므로 반드시 batch로 나눠야 한다.

```python
data_loader = DataLoader(
    data_set,
    batch_size=batch_size,
    shuffle=shuffle_flag,
    num_workers=args.num_workers,
    drop_last=drop_last)
```

- `data_set`: Dataset 클래스의 인스턴스
- `batch_size`: batch 하나에 들어가는 sample 수
- `shuffle`: epoch마다 데이터를 섞을지. 섞으면 모델이 데이터 순서에 맞춰 과적합되는 것을 줄일 수 있다
- `num_workers`: 데이터를 불러오는 subprocess 수. 여러 개를 쓰면 데이터를 비동기로 불러와 학습 중 대기 시간을 줄인다
- `drop_last`: 마지막 batch가 `batch_size`보다 작을 때 버릴지

### Dataset

데이터셋 클래스는 PyTorch의 `Dataset`을 상속받고, 시간 단위 데이터용과 분 단위 데이터용이 따로 있다. 생성할 때 받는 인자는 다음과 같다.

- `root_path`, `data_path`: 데이터 파일이 있는 폴더와 파일 이름
- `flag`: train, val, test 가운데 어느 구간을 쓸지
- `size`: `[seq_len, label_len, pred_len]`
- `features`: 예측 task의 종류. 기본값은 `M`
    - `M`: 다변량으로 다변량을 예측
    - `S`: 단변량으로 단변량을 예측
    - `MS`: 다변량으로 단변량을 예측
- `target`: `S`나 `MS`에서 예측 대상으로 쓸 열 이름. 기본값은 `OT`
- `timeenc`: 시각 정보를 어떤 방식으로 feature로 만들지. 0 또는 1
- `freq`: 시계열의 주기. 시간 단위는 `h`, 분 단위는 `t`. 시각 정보를 어느 단위까지 feature로 만들지 정한다. ETTh1은 시간 단위 데이터이므로 `h`이다

인자가 주어지지 않으면 기본값으로 설정하고, 설정이 끝나면 `__read_data__()`를 실행한다.

### \_\_read_data\_\_()

데이터를 읽고 전처리하는 부분이다.

1. **파일 읽기.** 주어진 경로의 CSV를 읽어 `df_raw`에 저장한다.
2. **구간 경계 설정.** `border1s`와 `border2s`라는 두 목록을 만든다. 같은 위치의 값이 짝을 이루고, `border1s`가 시작점, `border2s`가 끝점이다. train, val, test 세 구간이 여기서 나뉜다. 경계 값에 나오는 `12 * 30 * 24`는 12달, 30일, 24시간으로 읽힌다.
3. **열 선택.** 다변량(`M`, `MS`)이면 날짜 열을 뺀 모든 열을 쓰고, 단변량(`S`)이면 `target` 열만 쓴다.
4. **scaling.** scikit-learn의 `StandardScaler`로 데이터를 표준화한다. `fit`은 평균과 표준편차를 구해 저장하고, `transform`은 그 값으로 데이터를 평균 0, 표준편차 1이 되도록 바꾼다. 평균과 표준편차는 train 구간에서만 구하고, 변환은 전체 데이터에 적용한다.
5. **시각 feature 만들기.** `timeenc == 0`이면 날짜에서 month, day, weekday, hour를 뽑아 `data_stamp`에 넣는다. `timeenc == 1`이면 `time_features()` 함수로 만든다.
6. **저장.** 2번에서 정한 경계에 따라 잘라서 저장한다.

이렇게 만들어진 데이터셋이 `data_factory`를 거쳐 `Exp_Main`으로 돌아온다.

## 4. 학습 준비

데이터를 가져온 뒤 `train()`은 학습에 필요한 것들을 차례로 준비한다.

- checkpoint를 저장할 폴더 생성
- early stopping 설정
- optimizer 설정
- 손실 함수 설정. MSE를 쓴다
- AMP(Automatic Mixed Precision) 설정. 일부 연산을 16비트 부동소수점으로 계산해 메모리 사용과 학습 시간을 줄이는 기능이고, 인자로 켰을 때만 쓴다
- `OneCycleLR` 설정. 학습 도중 learning rate를 바꿔 주는 scheduler이다

## 5. PatchTST.py: 모델 클래스

`Exp_Main._build_model()`은 `PatchTST.Model`을 만든다. `models` 폴더의 `PatchTST.py` 파일 안에 `Model` 클래스가 있어서 `PatchTST.Model(...)`로 접근하는 구조이다. `Model`은 `nn.Module`을 상속받고, 인자 전체를 `configs`라는 이름으로 전달받는다. `nn.Module`을 상속한 클래스가 어떻게 동작하는지는 [PyTorch 기본](06-pytorch-nn-module.md)에 정리했다.

### 분해 옵션

`decomposition` 인자가 참이면 `series_decomp` 클래스로 입력을 추세와 나머지로 분해한다. 이 클래스는 `kernel_size`를 받아 이동평균을 계산한다. 분해를 켜면 backbone을 두 개(`model_trend`, `model_res`) 만들고, 끄면 하나(`model`)만 만든다.

`decomposition`의 기본값은 0이라, 기본 설정에서는 분해를 하지 않는다. 시계열 분해가 무엇인지는 [시계열 분해](08-time-series-decomposition.md)에 따로 적었다.

### PatchTST_backbone

backbone은 `layers/PatchTST_backbone.py`에 있고, 네 부분으로 구성된다.

**RevIN(Reversible Instance Normalization).** 시계열을 위한 정규화 방법이다. `revin` 인자로 켜고 끈다. 켜면 모델에 넣기 전에 정규화하고, 모델의 출력에는 반대 과정(denormalization)을 적용해 예측을 원래 scale로 되돌린다. 정규화 방법들의 차이는 [정규화 기법 네 가지](07-normalization-techniques.md)에 정리했다.

**Patching.** 관련된 설정은 다음과 같다.

- `patch_len`: patch 하나의 길이. 16
- `stride`: 다음 patch로 넘어갈 때 이동하는 간격. 8
- `padding_patch`: `'end'`이면 sequence 끝에 padding을 붙인다
- `context_window`: 입력 sequence의 길이(`seq_len`). 여기서는 96으로 놓고 계산했다

입력 길이가 96이고 patch 길이가 16, stride가 8이면 patch는 $(96 - 16) / 8 + 1 = 11$개가 만들어진다.

| patch | 1 | 2 | 3 | ⋯ | 10 | 11 |
|---|---|---|---|---|---|---|
| 시작 위치 | 0 | 8 | 16 | ⋯ | 72 | 80 |
| 끝 위치 | 16 | 24 | 32 | ⋯ | 88 | 96 |

`padding_patch == 'end'`이면 padding layer가 추가로 만들어진다.

```python
self.padding_patch_layer = nn.ReplicationPad1d((0, stride))
```

이 layer는 sequence의 마지막 값을 `stride`만큼 복사해 뒤에 붙인다. sequence가 8만큼 길어지므로 patch가 하나 더 만들어지고, 코드에서도 `patch_num`에 1을 더한다. 결과적으로 이 설정에서 patch는 12개가 된다.

**Backbone.** `TSTiEncoder`를 만든다. 이름의 i는 channel-independent를 뜻한다. channel에 관계없이 각 변수를 단변량 시계열로 다룬다는 의미이다. 구성은 Transformer의 encoder와 같다.

1. **Input encoding.** `nn.Linear(patch_len, d_model)`로 길이 `patch_len`인 patch를 `d_model` 차원 벡터로 바꾼다. ETTh1 설정에서는 `patch_len`과 `d_model`이 모두 16이다. `nn.Linear`는 fully connected layer(dense layer)이고, 입력 sample의 feature 수와 출력 sample의 feature 수를 받는다.

    ```python
    m = nn.Linear(20, 30)
    input = torch.randn(128, 20)
    output = m(input)
    print(output.size())    # torch.Size([128, 30])
    ```

2. **Positional encoding.** 학습 가능한 위치 encoding을 더해 patch의 시간 순서를 반영한다.
3. **Dropout.** 위 두 값을 더한 결과에 dropout을 적용한다.
4. **TSTEncoder.** 실질적인 Transformer encoder이다.

논문의 구조도에 대응시키면, 단변량 시계열 하나가 들어와 instance normalization과 patching을 거치고, 만들어진 patch sequence에 학습 가능한 선형 projection과 위치 encoding이 적용된 뒤 encoder로 들어가는 흐름이다.

`TSTEncoder`는 layer 수만큼 `TSTEncoderLayer`를 만들어 `nn.ModuleList`에 넣어 둔다. encoder layer를 몇 번 거칠지가 여기서 정해진다. `nn.ModuleList`는 module을 리스트처럼 보관하는 용도이다. 일반 파이썬 리스트에 넣으면 PyTorch가 그 module의 존재를 알지 못해 parameter가 등록되지 않으므로, `ModuleList`에 넣어 module이 있다는 것을 PyTorch에 알려야 한다. `nn.Sequential`과 달리 `ModuleList`는 들어 있는 module의 `forward()`를 대신 불러 주지 않으며, `TSTEncoder`의 `forward()`가 for 문으로 layer를 하나씩 호출한다.

attention은 `res_attention` 옵션이 켜진 상태가 기본이다. 이전 layer의 attention score를 다음 layer의 score에 더해 주는 방식이다.

**Head.** `pretrain_head`가 참이면 사전학습용 head를 쓰고, `head_type`이 `flatten`이면 `Flatten_Head`를 쓴다. 기본은 `flatten`이다. Flatten, Linear, Dropout을 차례로 거쳐 최종 예측을 만든다.

## 6. 정리: forward의 순서

여기까지 읽고 알게 된 것은, 코드의 구조가 논문의 설명과 그대로 대응된다는 점이다. forward는 다음 순서로 진행된다.

1. instance normalization
2. patching
3. 모델(backbone과 head)
4. denormalization. 예측을 원래 scale로 되돌린다

결과가 반환된 뒤에 남는 것은 loss를 구하고, gradient를 계산하고, parameter를 갱신하는 일반적인 학습 과정이다.

## 남았던 질문과 답

공부할 당시에는 풀지 못하고 남겨 둔 질문들이다. 글을 정리하면서 답을 찾아 덧붙였다.

**Q. `Exp_Basic`에는 `_build_model()`이 구현되어 있지 않은데 어떻게 호출되는가.**

`Exp_Basic`에도 `_build_model()`이 선언되어 있기는 하다. 다만 내용이 `raise NotImplementedError`뿐이어서, 하위 클래스가 반드시 다시 정의하도록 자리만 잡아 둔 것이다. `Exp_Main(args)`로 객체를 만들면 `Exp_Basic.__init__`이 실행되지만 이때의 `self`는 `Exp_Main` 객체이다. 파이썬은 메서드를 객체의 클래스에서 먼저 찾으므로 `self._build_model()`은 `Exp_Main`에서 다시 정의한 메서드를 호출한다.

**Q. 경계 값의 `12 * 30 * 24`는 무엇이고, 31일인 달은 어떻게 처리하는가.**

실제 달력을 따르는 값이 아니다. 한 달을 30일로 어림해 구간의 길이를 고정한 것이다. ETT의 시간 단위 데이터는 앞에서부터 12개월(8,640시간)을 train, 다음 4개월을 val, 그다음 4개월을 test로 쓴다. 31일인 달을 따로 처리하지 않으며, 경계는 날짜가 아니라 행 번호이다. val과 test의 시작점에서 `seq_len`을 빼는 것은 그 구간의 첫 예측에도 입력으로 쓸 과거 값이 필요하기 때문이다.

**Q. `timeenc == 1`은 무엇을 하는가.**

`timeenc`는 `--embed` 인자로 정해진다. `embed`가 `timeF`(기본값)이면 1, 아니면 0이다. `timeenc == 1`일 때 쓰는 `time_features()`는 시각의 각 성분(시, 요일, 일, 월 등)을 $-0.5$에서 $0.5$ 사이의 실수로 바꾼다. 어느 성분까지 만들지는 `freq`에 따라 달라진다. `timeenc == 0`이면 같은 성분을 정수 그대로 쓴다. 다만 PatchTST는 모델에 입력 시계열만 넣고 이 시각 feature는 쓰지 않는다. 이 feature는 같은 코드로 실행하는 다른 Transformer 계열 모델에서 쓰인다.

**Q. `label_len`은 무엇인가.**

인자 설명에는 "start token length"라고 적혀 있다. Informer 계열 모델에서 decoder에 넣어 주는 입력 구간의 길이이다. 데이터셋은 예측 구간 앞에 `label_len`만큼의 과거 값을 붙여 정답 sequence를 만들고, loss를 계산할 때는 뒤쪽 `pred_len` 구간만 쓴다. decoder가 없는 PatchTST에서는 의미가 없는 값이다.

**Q. `decomposition`은 왜 기본값이 0인가.**

논문의 모델 구조에는 분해가 들어 있지 않고, 코드에서는 선택 옵션으로만 제공된다. 켜면 추세와 나머지를 각각 별도의 backbone에 넣고 두 예측을 더한다. 이 옵션을 켰을 때 성능이 어떻게 달라지는지는 [프로젝트](../../projects/ai-course/03-patchtst.md)에서 실험했다.

**Q. instance normalization을 시계열에는 어떻게 적용하는가.**

RevIN은 입력 window 하나를 instance로 보고, 변수(channel)마다 시간 축 방향의 평균과 표준편차를 구해 정규화한다. 즉 길이 `seq_len`인 입력 구간 하나가 자기 자신의 통계량으로 정규화된다. 이때 구한 평균과 표준편차를 저장해 두었다가 모델의 출력에 곱하고 더해 원래 scale로 되돌린다. 예측 결과를 원래 scale에서 확인하려는 것만이 목적은 아니고, 구간마다 평균과 분산이 달라지는 시계열의 분포 변화를 모델이 직접 다루지 않아도 되게 하는 것이 본래 목적이다.

**Q. fully connected layer인 `nn.Linear`가 왜 input encoding에 쓰이는가.**

patch 하나는 길이 16인 벡터이고, Transformer에 넣으려면 `d_model` 차원의 벡터로 바꿔야 한다. 자연어에서는 단어 번호를 embedding 표에서 찾아 벡터로 바꾸지만, patch는 이미 실수 벡터이므로 선형 변환 한 번으로 `d_model` 차원에 사상한다. 논문에서 $\mathbf{W}_p$로 적는 학습 가능한 선형 projection이 이 layer이다.

**Q. `nn.Linear` 예제에서 입력의 128은 sample의 개수인가.**

그렇다. 마지막 차원이 feature 수이고 앞의 차원은 그대로 유지된다. feature가 20개인 sample 128개가 들어가 feature가 30개인 sample 128개가 나온다.

**Q. embedding에 dropout은 왜 적용하는가.**

과적합을 줄이기 위한 regularization이다. 원래의 Transformer도 embedding과 positional encoding을 더한 값에 dropout을 적용한다.

**Q. padding layer는 만들어 두기만 하고 나중에 쓰는가.**

그렇다. `__init__`에서는 layer를 만들기만 하고, `forward()`에서 patch를 나누기 직전에 입력에 적용한다.

**Q. `ModuleList`에 넣기 전까지는 PyTorch를 거치지 않는 것인가.**

아니다. `Model`, `PatchTST_backbone`, `TSTiEncoder` 모두 `nn.Module`을 상속한 클래스이고, `nn.Module`은 자기 속성으로 대입된 하위 module을 자동으로 등록한다. 문제가 되는 것은 module을 파이썬 리스트에 담을 때뿐이다. 리스트 안의 module은 자동으로 등록되지 않으므로 `ModuleList`를 쓴다.

## 참고 자료

- Y. Nie, N. H. Nguyen, P. Sinthong, J. Kalagnanam, "A Time Series is Worth 64 Words: Long-term Forecasting with Transformers", ICLR 2023. <https://arxiv.org/abs/2211.14730>
- PatchTST 공식 구현. <https://github.com/yuqinie98/PatchTST>
- T. Kim, J. Kim, Y. Tae, C. Park, J.-H. Choi, J. Choo, "Reversible Instance Normalization for Accurate Time-Series Forecasting against Distribution Shift", ICLR 2022. <https://openreview.net/forum?id=cGDAkQo1C0p>
- Python 공식 문서, "argparse". <https://docs.python.org/3/library/argparse.html>
