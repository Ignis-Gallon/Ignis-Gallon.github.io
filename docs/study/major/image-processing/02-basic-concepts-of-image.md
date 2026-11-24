---
title: "Basic Concepts of Image"
date: 2023-10-03
last_modified_at: 2024-11-22
category: major
tags: [영상처리, Image Restoration, Indexed Color, Mach Bands]
---

# Basic Concepts of Image

## &lt;1.5 Some Applications&gt;

- 영상처리는 여러 분야에서 사용되고 있다.

    - Medicine : 검사나 상태 확인 등을 할 때 x-rays, MRI, CT 등을 사용한다.
    - Agriculture : 위성 사진 등
    - Industry : 상태 검사 등
    - Law enforcement (by law) : 지문 검사, 속도 측정 카메라 등

---

## &lt;1.6 Aspects of Image Processing&gt;

### Aspect types

- **Image Enhancement**

    - Sharpening or Deblurring
    - Edge 강조
    - 대조 강화, 밝기 강화
    - 노이즈 제거

- **Image Restoration**

    - Removing of Blur caused by linear motion
    - Removal of optical distortions (사진에 나온 밝은 부분 등의 제거)
    - Removing periodic interference : 주파수 분석을 통해 반복적으로 나오는 문제 제거

### Restoration

- **deraining**

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/01.webp)

비가 내리고 있는 사진에서 비를 제거하는 기법

- **deinterlacing**

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/02.webp)

충분하지 않은 프레임으로 보이는 영상의 프레임을 높여주는 기법

- **demosaicking**

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/03.webp)

어떤 피사체를 사진으로 찍으면, color channel을 구분해서 픽셀에 담기게 된다. 한 픽셀에 빨간 색만 있다는 것은, 다른 픽셀은 빨간색이 비어있다는 것인데, 그렇다면 다른 빈 영역의 색깔을 생성해 내어야 한다. 이때 어떤 알고리즘을 쓰냐에 따라서 오류가 없는 영상이 만들어질 수도 있고, 오류가 있는 영상이 만들어질 수도 있다.

- **super-resolution(초해상도, 초고해상도)**

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/04.webp)

Original 영상이 있을때, 이 영상을 확대하고자 하는 경우이다. Nearest Neighbor 방식을 활용한다면 확대가 깔끔하게 되지는 않지만, Super-resolution 방식을 활용하면 더욱 깨끗한 확대 영상을 얻을 수 있다.

---

## &lt;1.6 Aspects of Image Processing&gt;

### Image Segmentation

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/05.webp)

- finding lines, circles, or particular shapes in an image, and : **Object Detection**
- identifying cars, trees, building, or roads in an aerial photograph(항공 사진)

---

## &lt;1.7 An Image-Processing Task&gt;

### Image-Processing의 작업

- Acquiring the image : 영상을 얻어오기
- Pre-processing and post-processing : 전처리/후처리
- Segmentation : 영상 분할
- Representation and description : 영상 표현 및 설명
- Recognition and interpretation : 영산 인식 및 해석

### Binary Image

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/06.webp)

- 1은 밝은 부분, 0은 어두운 부분

### Gray Scale Image

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/07.webp)

### True color or reg-green-blue

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/08.webp)

- dTrue color(RGB) : 24 bit color(R, G, B 각각에 8bit를 부여한 색)

### Indexed Color

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/09.webp)

- 자주 나오는 Color를 테이블을 만들어 보관하고, 자주 나오지 않는 것은 보관하지 않는 것이다. Gray Scale Image의 경우, 한 픽셀을 구성하기 위해서는 8bit가 필요하다. 그런데 만약 이 영상이 자주 나오는 색깔로만 이루어진다면, 자주 나오는 값을 Table에 저장해두고 색깔에 Indexing을 하면 더욱 효과적으로 처리할 수 있다. (일종의 Referencing)

### Indexed color vs True color

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/10.webp)

- 4-color : 2bit
- 16-color : 4bit
- 256-color : 8bit (gif)
- True color : 24bit (16,777,216 개의 색깔 표현 가능)
- Indexed Color는 일종의 Palette 개념과도 같음

---

## &lt;1.9 Image File Sizes&gt;

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/11.webp)

- 어떤 Color 영상이 있을때, 크기가 512x512이고, 채널은 3개이다. (RGB 세 개의 채널이 있다.)

- 512\*512\*3 = 786,432 bytes = 786.43 Kb, 약 0.786 Mb

    - 한 채널을 표현하는데 1바이트이고, 가로 세로 크기가 512, 512이기 때문에 모두 곱하면 됨

---

## &lt;1.10 Image Perception&gt;

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/12.webp)

- 사람의 눈은 한계가 존재한다.

    - 색의 강도는 배경색에 따라서 달라진다.
    - 그래서 실제로는 존재하지 않는 착시현상을 보게될 수도 있다.

### Mach Bands

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/13.webp)

- 가운데 네모는 색깔이 동일하지만, 배경색이 밝으면 더욱 어둡게 보이고, 배경색이 어두우면 더욱 밝게 보인다. 이와 같이 **점차적으로 대비가 감소하는 띠가 서로 인접해있을 때, 띠의 경계에서 색이 더 진해보이거나 더 밝게 보이는데,** 이를 Mach bands라고 한다.

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/14.webp)

- 이와 같이 색이 변하는 Gradation을 보면, 색이 변하는 부분에서 선이 생기는 부분이 보이는 착시 현상이 발생하는데, 이를 Mach Bands라고 볼 수 있다.
- Optical illusion의 예(착시현상, Mach Bands 효과에 기인한다.)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/15.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/16.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/17.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/18.webp)

- 왼쪽이 밝으면, 오른쪽은 더 어두워 보이고, 오른쪽이 더 어두우면, 왼쪽이 더 밝고… 사람의 눈은 이런 식으로 인식하게 된다.
- 아래에서 서로 분리시켜놓은 것과 비교해 보면, 색깔 바를 붙여놓은 것의 색깔이 조금 다르게 보이는 것을 알 수 있다.
- Mach Bands 예시

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/19.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/20.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/21.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/22.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/23.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/24.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/25.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/26.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/27.webp)

### Gamma Correction

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/28.webp)

- 이전에 우리 몸은 Low Pass Filter, Log Scale, 녹색에 민감하고, 색상보다 밝기에 민감하다고 했다.
- 우리가 사진을 찍을 때 어떠한 현상이 일어나게 되는데, 우리가 보기에는 오른쪽 다섯 개의 사진 중에서 감마가 1/3, 혹은 1/2인 사진이 가장 그럴듯하게 보인다. 하지만 실제 디바이스로 사진을 찍으면 상대적으로 어둡게 촬영된다. ⇒ 이것이 좌측에 나온 그래프이다.
- 따라서 우리가 사진은 원하는 대로 보기 위해서는 (우측 그래프) 보정을 해 줄 필요가 있는데, 가운데 그래프를 더함으로써 밝게 만들 수 있다. ⇒ 이를 **Gamma Correction**이라고 한다.

### Hearing range(가청 주파수)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/29.webp)

- Sound & Audio Frequency

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/30.webp)

- C-major Scale 1

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/31.webp)

A가 440Hz일 때, 하나 높은 A 건반으로 가기 위해서는 12개의 건반을 눌러야 한다. Frequency로 볼 때, 한 옥타브가 높은 건반은 주파수가 2배 높은 것이다.

따라서 낮은 C의 주파수가 262라고 한다면, C#이 되기 위해서는 262 \* 2^(1/12)를 해야 한다.

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/32.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/33.webp)

### Excercises

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/34.webp)

---

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/35.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/36.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/37.webp)

![](../../../assets/img/study/major/image-processing/02-basic-concepts-of-image/38.webp)
