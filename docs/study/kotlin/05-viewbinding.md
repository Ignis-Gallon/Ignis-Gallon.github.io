---
title: "viewBinding"
date: 2022-09-25
last_modified_at: 2022-09-27
category: study
tags: [Kotlin, Android, ViewBinding]
---

# viewBinding

작성일 2022-09-25 · 최종 수정일 2022-09-27
{ .post-date }

## ViewBinding

- **역할**: ViewBinding은 XML 레이아웃 파일에 대한 유형 안전한 바인딩 클래스를 자동으로 생성한다. 이를 통해 `findViewById`를 사용하지 않고도 레이아웃 내의 뷰에 직접 접근할 수 있다.

- **특징**:

    - `findViewById`를 대체하여 뷰를 찾는 작업을 간소화하고, 컴파일 시에 뷰의 타입을 안전하게 체크할 수 있다.
    - NullPointerException을 방지하며, 레이아웃의 각 뷰에 대한 객체 참조를 제공한다.

- **활용 방법**:

    - 프로젝트의 `build.gradle` 파일에서 ViewBinding을 활성화한다.

        ```groovy
        android {
            ...
            viewBinding {
                enabled = true
            }
        }
        ```

    - 바인딩 객체를 사용하여 레이아웃 뷰에 접근한다.

        ```kotlin
        val binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        binding.textView.text = "Hello, ViewBinding!"
        ```

## ViewBinding 활용 예시

1. **XML 레이아웃 파일 (**`activity_main.xml`**)**:

    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:id="@+id/textView"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hello, ViewBinding!" />
    </LinearLayout>
    ```

2. **Activity 파일 (**`MainActivity.kt`**)**:

    ```kotlin
    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle
    import com.example.viewbindingexample.databinding.ActivityMainBinding

    class MainActivity : AppCompatActivity() {
        private lateinit var binding: ActivityMainBinding

        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            binding = ActivityMainBinding.inflate(layoutInflater)
            setContentView(binding.root)

            // ViewBinding을 통해 뷰에 접근
            binding.textView.text = "ViewBinding을 사용하여 텍스트 변경"
        }
    }
    ```

위의 예시에서 ViewBinding은 레이아웃 파일에 정의된 뷰에 대해 안전하고 간단하게 접근할 수 있도록 해준다.
