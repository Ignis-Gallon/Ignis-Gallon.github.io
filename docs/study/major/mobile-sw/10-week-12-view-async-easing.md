---
title: "12주차: View 요소 추가, 비동기, Easing"
date: 2024-05-30
last_modified_at: 2024-05-31
category: major
tags: [모바일SW, Xamarin.Forms, 비동기, 애니메이션, C#]
---

# 12주차: View 요소 추가, 비동기, Easing

작성일 2024-05-30 · 최종 수정일 2024-05-31
{ .post-date }

## &lt;Tizen 강의&gt;

### StackLayout에 View 요소 추가하기

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace TizenXamlApp7
{
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();

            var layout = new StackLayout();
            var label1 = new Label
            {
                Text = "Hello World! C#!",
                TextColor = Color.Red,
                FontSize = 200,
                HorizontalOptions = LayoutOptions.StartAndExpand,
                VerticalOptions = LayoutOptions.StartAndExpand
            };

            var label2 = new Label
            {
                Text = "Hello World! C#!",
                TextColor = Color.Blue,
                FontSize = 200,
                HorizontalOptions = LayoutOptions.CenterAndExpand,
                VerticalOptions = LayoutOptions.CenterAndExpand
            };

            var label3 = new Label
            {
                Text = "Hello World! C#!",
                TextColor = Color.Green,
                FontSize = 200,
                HorizontalOptions = LayoutOptions.EndAndExpand,
                VerticalOptions = LayoutOptions.EndAndExpand
            };

            layout.Children.Add(label1);
            layout.Children.Add(label2);
            layout.Children.Add(label3);
            Content = layout;
        }
    }
}
```

### AbsoluteLayout에 View 요소 추가하기

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace TizenXamlApp7
{
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();

            var layout = new StackLayout();
            //var label1 = new Label
            //{
            //    Text = "Hello World! C#!",
            //    TextColor = Color.Red,
            //    FontSize = 200
            //};
            // AbsoluteLayout.SetLayoutBounds(label1, new Rectangle(0, 0, 800, 200));

            var image = new Image()
            {
                Source="icon.png",
                WidthRequest=100,
                HeightRequest=100,
                HorizontalOptions=LayoutOptions.StartAndExpand,
                VerticalOptions=LayoutOptions.StartAndExpand
            };

            var label3 = new Label
            {
                Text = "Hello World! C#!",
                TextColor = Color.Green,
                FontSize = 200
            };

            layout.Children.Add(image);
            //layout.Children.Add(label2);
            //layout.Children.Add(label3);
            Content = layout;
        }
    }
}
```

### AbsoluteLayout에서 여러 View 요소 추가하기

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace TizenXamlApp7
{
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();

            var layout = new AbsoluteLayout();

            var image_wall = new Image()
            {
                Source = "wall.jpg"
            };

            AbsoluteLayout.SetLayoutBounds(image_wall, new Rectangle(0, 0, 1920, 1080));

            var box1 = new BoxView()
            {
                Color = Color.Black
            };
            AbsoluteLayout.SetLayoutBounds(box1, new Rectangle(70, 70, 660, 660));

            var image3 = new Image()
            {
                Source = "icon.png"
            };
            AbsoluteLayout.SetLayoutBounds(image3, new Rectangle(100, 100, 600, 600));

            layout.Children.Add(image_wall);
            layout.Children.Add(box1);
            layout.Children.Add(image3);
            Content = layout;
        }
    }
}
```

### Asynchronous Method

FadeTo, RotateTo와 같은 ViewExtensions Method는 비동기적으로 동작한다. 이러한 것들은 모두 쓰레드로 구성되어 비동기적으로 수행되도록 설계되었다. 따라서 async 키워드로 설계된 메서드를 await 키워드를 통해 제어해야 한다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace TizenXamlApp7
{
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();
            this.FadeEffect();
        }
        private async void FadeEffect()
        {
            await label1.FadeTo(0, 5000);
            await label1.FadeTo(1, 5000);
        }
    }
}
```

⇒ 이와 같이 async keyword를 사용하여 비동기적으로 돌아가도록 설정

### 비동기 예제

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace TizenXamlApp7
{
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();
            label1.Opacity = 0;
            label2.Opacity = 0;
            FadeEffect();
        }
        private async void FadeEffect()
        {
            await label1.FadeTo(1, 5000);
            await label2.FadeTo(1, 5000);
            await image.RotateTo(360, 5000);
        }
    }
}
```

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace TizenXamlApp7
{
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();

            FadeEffect();
        }

        public static async void MarqueeEffect(Label label, double translatePosition)
        {
            await label.TranslateTo(-translatePosition, 0, 3000);
            label.TranslationX = translatePosition;
            await label.TranslateTo(0, 0, 3000);
        }

        public static async void MarqueeEffect3(Label label, double translatePosition)
        {
            await label.TranslateTo(0, -translatePosition, 3000);
            label.TranslationY = translatePosition;
            await label.TranslateTo(0, 0, 3000);
        }

        private async void FadeEffect()
        {
            double translatePosition = label1.Width + label1.X;

            //await label1.FadeTo(1, 5000);
            //await label2.FadeTo(1, 5000);
            //await image.RotateTo(360, 5000);

            await label1.TranslateTo(-translatePosition, 0, 5000);
            label1.TranslationX = translatePosition;
            await label1.TranslateTo(0, 0, 5000);
        }
    }
}
```

### 타이머 사용 예제

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace TizenXamlApp7
{
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class MainPage : ContentPage
    {
        private int readySecond = 10;
        private bool firstInitFlag = false;

        public MainPage()
        {
            InitializeComponent();
            SetTimer();
        }
        private void TimerElapsedEvent(object sender, System.Timers.ElapsedEventArgs e)
        {
            Console.WriteLine("Timer Elapsed Event is invoked...");
            FadeEffect(label1);
        }

        private async void SetTimer()
        {
            Console.WriteLine("Ready For Timer Set!");
            await Task.Delay(5000);
            Console.WriteLine("Timer is Set!");

            System.Timers.Timer timer = new System.Timers.Timer();
            timer.Interval = 10000;
            timer.Elapsed += TimerElapsedEvent;
            timer.Start();
        }

        private async void FadeEffect(Label label)
        {
            //if (firstInitFlag == false)
            //{
            //    Console.WriteLine("Wait!");
            //    await Task.Delay(readySecond * 1000);
            //    firstInitFlag = true;
            //    Console.WriteLine("End!");
            //}
            //else
            //{
            //    Console.WriteLine("No Wait!");
            //}

            double translatePosition = label.Width + label.X;
            Console.WriteLine("translatePosition: {0}", translatePosition);

            await label.TranslateTo(-translatePosition, 0, 5000);
            label.TranslationX = translatePosition;
            Console.WriteLine("label.TranslationX: {0}", translatePosition);
            await label.TranslateTo(0, 0, 5000);

            /* 
             * 아무래도 TranslationX는 현재 지정된 위치에서 X 방향으로 translatePosition 만큼 이동하는 것 같다.
             * TranslateTo를 했다고 해서 Label이 갖는 그 실제 위치가 변화하지는 않는 것으로 추정된다.
             */
        }
    }
}
```

### Easing 예제

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace TizenXamlApp7
{
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class MainPage : ContentPage
    {
        private int readySecond = 10;
        private bool firstInitFlag = false;

        public MainPage()
        {
            InitializeComponent();
            SetTimer();
        }
        private void TimerElapsedEvent(object sender, System.Timers.ElapsedEventArgs e)
        {
            Console.WriteLine("Timer Elapsed Event is invoked...");
            MarqueeEffect(label1, 500);
        }

        private async void SetTimer()
        {
            Console.WriteLine("Ready For Timer Set!");
            await Task.Delay(5000);
            Console.WriteLine("Timer is Set!");

            System.Timers.Timer timer = new System.Timers.Timer();
            timer.Interval = 10000;
            timer.Elapsed += TimerElapsedEvent;
            timer.Start();
        }

        public static async void MarqueeEffect(Label label, double translatePosition)
        {
            await label.TranslateTo(-translatePosition, 0, 3000, Easing.BounceIn);
            label.TranslationX = translatePosition;
            await label.TranslateTo(0, 0, 3000, Easing.BounceOut);
        }

        private async void FadeEffect(Label label)
        {
            //if (firstInitFlag == false)
            //{
            //    Console.WriteLine("Wait!");
            //    await Task.Delay(readySecond * 1000);
            //    firstInitFlag = true;
            //    Console.WriteLine("End!");
            //}
            //else
            //{
            //    Console.WriteLine("No Wait!");
            //}

            double translatePosition = label.Width + label.X;
            Console.WriteLine("translatePosition: {0}", translatePosition);

            await label.TranslateTo(-translatePosition, 0, 5000);
            label.TranslationX = translatePosition;
            Console.WriteLine("label.TranslationX: {0}", translatePosition);
            await label.TranslateTo(0, 0, 5000);

            /* 
             * 아무래도 TranslationX는 현재 지정된 위치에서 X 방향으로 translatePosition 만큼 이동하는 것 같다.
             * TranslateTo를 했다고 해서 Label이 갖는 그 실제 위치가 변화하지는 않는 것으로 추정된다.
             */
        }
    }
}
```

```csharp
private async void ImageAnimation()
{
	await image.RotateTo(720, 3000, Easing.CubicInOut);
	await image.TranslateTo(1720, 0, 3000, Easing.CubicInOut);
}
```

> Animation은 스레드로 동작하므로 동시/구분을 모두 할 수 있다. 그런데 만약 일부는 동시에 같이 효과를 주고, 일부는 차례로 주려면 어떻게 해야 할까?

### Task.WhenAny

Task.WhenAny를 사용하면 여러 개의 스레드 작업을 동시에 수행할 수 있다. 이것은 UI에서만 동작하는게 아니라 C# 자체에서 동작하며, 이를 단지 UI에 적용했을 뿐이다.

```xml
<?xml version="1.0" encoding="utf-8" ?>

<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TizenXamlApp7.MainPage">
    <ContentPage.Content>
        <StackLayout>
            <Image
                x:Name="image"
                Source="icon.png"
                WidthRequest="200"
                HeightRequest="200"
                HorizontalOptions="Start"
                VerticalOptions="CenterAndExpand">
            </Image>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

```csharp
public MainPage()
        {
            InitializeComponent();
            // SetTimer();
            ImageAnimation();
        }

        private async void ImageAnimation()
        {
            await Task.Delay(5000);

            await Task.WhenAny<bool>
            (
                image.RotateTo(720, 3000, Easing.CubicInOut),
                image.TranslateTo(1720, 0, 3000, Easing.CubicInOut)
            );
        }
```

### Button

```xml
<?xml version="1.0" encoding="utf-8" ?>

<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TizenXamlApp7.MainPage">
    <ContentPage.Content>
        <StackLayout>
            <Label
                x:Name="label"
                Text="Hello"
                TextColor="White"
                FontSize="200"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="CenterAndExpand">
            </Label>
            <Button
                Text="Button"
                Clicked="buttonClicked"
                VerticalOptions="FillAndExpand"
                HorizontalOptions="FillAndExpand">
            </Button>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

```csharp
public MainPage()
        {
            InitializeComponent();
            // SetTimer();
            // ImageAnimation();
        }
        private void buttonClicked(object sender, EventArgs args)
        {
            Random generator = new Random();
            int r = generator.Next(0, 255);
            int g = generator.Next(0, 255);
            int b = generator.Next(0, 255);

            label.TextColor = Color.FromRgb(r, g, b);
        }
```

### TapGestureRecognizer

```csharp
public MainPage()
        {
            InitializeComponent();
            // SetTimer();
            // ImageAnimation();

            var tapGesture = new TapGestureRecognizer();
            tapGesture.Tapped += labelClicked; // EventHandler
            label.GestureRecognizers.Add(tapGesture);
        }

		        private void labelClicked(object sender, EventArgs args)
		        {
		            Random generator = new Random();
		            int r = generator.Next(0, 255);
		            int g = generator.Next(0, 255);
		            int b = generator.Next(0, 255);
		
		            label.TextColor = Color.FromRgb(r, g, b);
		        }
				}
```

> 전달받은 object에 대해서 변화를 적용하려면, 전달된 object sender를 참조해야만 한다. 다음과 같이 구현이 가능하다.

```csharp
private void labelClicked(object sender, EventArgs args)
{
	var label = (Label)sender;
	
	// ...
	label.TextColor = Color.FromRgb(r, g, b);
}

// ...

private void labelClicked(object sender, EventArgs args)
{
	var label = sender as Label;
	
	// ...
	
	label.TextColor = Color.FromRgb(r, g, b);
}
```
