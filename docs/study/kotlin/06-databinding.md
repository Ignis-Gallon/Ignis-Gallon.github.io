---
title: "dataBinding"
date: 2022-09-25
last_modified_at: 2022-09-27
category: study
tags: [Kotlin, Android, DataBinding]
---

# dataBinding

## DataBinding

- **역할**: DataBinding은 XML 레이아웃 파일에서 뷰와 데이터 소스를 직접 연결할 수 있도록 한다. 이를 통해 UI 요소와 데이터의 양방향 바인딩이 가능해진다.

- **특징**:

    - XML 레이아웃에서 변수 및 표현식을 사용하여 뷰와 데이터를 바인딩할 수 있다.
    - 데이터 변경 시 UI를 자동으로 업데이트하며, 더 복잡한 데이터 로직을 처리하는 데 적합하다.

- **활용 방법**:

    - 프로젝트의 `build.gradle` 파일에서 DataBinding을 활성화한다.

        ```groovy
        android {
            ...
            dataBinding {
                enabled = true
            }
        }
        ```

    - XML 레이아웃에서 `<layout>` 태그를 사용하여 데이터 바인딩을 설정하고, Activity나 Fragment에서 바인딩 객체를 사용한다.

        ```xml
        <layout xmlns:android="http://schemas.android.com/apk/res/android">
            <data>
                <variable
                    name="user"
                    type="com.example.User" />
            </data>
            ...
        </layout>
        ```

        ```kotlin
        val binding: ActivityMainBinding = DataBindingUtil.setContentView(this, R.layout.activity_main)

        val user = User("John Doe")
        binding.user = user
        ```

## DataBinding 활용 예시

1. **XML 레이아웃 파일 (**`activity_main.xml`**)**:

    ```xml
    <layout xmlns:android="http://schemas.android.com/apk/res/android">
        <data>
            <variable
                name="user"
                type="com.example.databindingexample.User" />
        </data>
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@{user.name}" />
        </LinearLayout>
    </layout>
    ```

2. **데이터 클래스 (**`User.kt`**)**:

    ```kotlin
    data class User(val name: String)
    ```

3. **Activity 파일 (**`MainActivity.kt`**)**:

    ```kotlin
    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle
    import androidx.databinding.DataBindingUtil
    import com.example.databindingexample.databinding.ActivityMainBinding

    class MainActivity : AppCompatActivity() {
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)

            val binding: ActivityMainBinding = DataBindingUtil.setContentView(this, R.layout.activity_main)
            val user = User("John Doe")
            binding.user = user
        }
    }
    ```

위의 예시에서 DataBinding은 XML 레이아웃 파일에서 직접 데이터 객체를 바인딩하고 표현할 수 있게 해준다.
