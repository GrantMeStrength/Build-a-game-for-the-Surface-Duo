# Writing a game for the Surface Duo

If the release of the Microsoft Surface Duo has tempted you to take a look at Android development, you might be wondering where to begin. Is it really necessary to learn Java, and an entirely different way of working with new controls and design patterns?

Well, not necessarily. [Xamarin](https://xamarin.github.io) is Microsoft's .NET cross-platform development platform, and it's a great way to write apps for the Surface Duo (and other mobile devices) using tools you probably already know: Visual Studio, C#, and XAML.

<img src="https://github.com/GrantMeStrength/Build-a-game-for-the-Surface-Duo/blob/main/game.png" alt="Everyone loves a good Ninja Cat game" width="200"/>

Being old-school, I judge how useful a device and platform is by how easy it is to write a game. The good news is that with a few extra libraries, Xamarin is a great choice for writing casual games and I've already got some prototypes running on my Duo.

This document will walk you through creating a Visual Studio project that's ready to start using as the basis of your first Duo game. Or you can simply download the Visual Studio projects and get a head start.

## Stage 1 - Install all the tools

In order to create an Xamarin app for the Surface Duo, you will need to:

1. Install [Visual Studio](https://visualstudio.microsoft.com/) (the Community Edition is fine)
1. Install the [Surface Duo SDK](https://docs.microsoft.com/en-us/dual-screen/android/get-duo-sdk?tabs=windows) (which includes the emulator)

Installing the SDK can be a little involved. Pay particular attention to how the emulator is launched (in case you don't have your Duo just yet).

## Create a new Visual Studio project

Let's create a new project, ready to be the basis of your first Xamarin game.

1. Launch Visual Studio, click on **Create a new project** and select the **Mobile App (Xamarin.Forms)** project type.

<img src="https://github.com/GrantMeStrength/Build-a-game-for-the-Surface-Duo/blob/main/pic-01.png" alt="The Mobile App (Xamarin.Forms) is the project type to use." width="400"/>



2. Click **Next**, name the project **DuoGame** and click **Create**.
3. You now need to select a template for the app. Pick **Blank**, and enable Android and disable the iOS and Windows options (unless you want to go truly cross-platform, of course). 
4. Click **Create**.

<img src="https://github.com/GrantMeStrength/Build-a-game-for-the-Surface-Duo/blob/main/template.png" alt="Select the Blank template." width="400"/>


## Add the code libraries you will need

The secret to writing games is to pick a library to do all the hard work for you. We're going to use [SkiaSharp](https://github.com/mono/SkiaSharp) for this project, which is distributed in form of a few NuGet libraries.

1. In the Solution Explorer view, find the primary project - **DuoGame** - which is immediately under  **Solution 'DuoGame'**, then right-click it and select **Manage NuGet Packages...**.

<img src="https://github.com/GrantMeStrength/Build-a-game-for-the-Surface-Duo/blob/main/nuget.png" alt="Remember to tap Browse when looking for NuGet packages." width="400"/>


2. Click on the **Browse** tab, and then search for and install:

    * **SkiaSharp**
    * **SkiaSharp.Views.Forms**
   
3. Select **DuoGame.Android**, right-click and select **Manage NuGet Packages..**. Again, go to the **Browse** tab, and search for and install:

    * **Xamarin.DuoSdk**
    * **Xamarin.Forms.DualScreen**

If all the NuGet packages are installed properly, your Solution Explorer should look like this:

<img src="https://github.com/GrantMeStrength/Build-a-game-for-the-Surface-Duo/blob/main/solutionexplorer.png" alt="The Solution Explorer view after the NuGet packages are installed." width="400"/>


## Create, initialize and build the first view

Now we can start to add some C# code to the project to initialize the various views and libraries.

Under the **DuoGame.Android** project, look for **MainActivity.cs** and change the **MainActivity** class to read like this:

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {

 protected override void OnCreate(Bundle savedInstanceState)
        {
            TabLayoutResource = Resource.Layout.Tabbar;
            ToolbarResource = Resource.Layout.Toolbar;

            base.OnCreate(savedInstanceState);

            Xamarin.Essentials.Platform.Init(this, savedInstanceState);
            global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
            LoadApplication(new App());
        }
        public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
        {
            Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

            base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
        }
      
    }
```

The bulk of your game code will be in the main **DuoGame** project, rather than the Android-specific project. Find and open **MainPage.xaml** as this will be where we set up the *canvas* where all our drawing takes place.

Change the XAML to look like this.

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
             x:Class="DuoGame.MainPage">

    <Grid >

        <skia:SKCanvasView x:Name="canvasView"  PaintSurface="OnCanvasViewPaintSurface" EnableTouchEvents="True" Touch="OnTouch">
        </skia:SKCanvasView>

    </Grid>

</ContentPage>

```

There's a draw and a touch event in this code, and so we'll need to add the methods to the code-behind. Open up **MainPage.xaml.cs**, and replace what's there with the following:

```csharp
using SkiaSharp.Views.Forms;
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Xamarin.Forms;

namespace DuoGame
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();
        }
        void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
        {

        }

        private void OnTouch(object sender, SKTouchEventArgs e)
        {

        }
    }
}
```

And that is the basic framework in place. The project should build (although it won't do anything yet), and you should be able to run it on both the emulator and any Android device you have handy. If you do connect an Android device, remember there are some steps to activate Developer Mode and support debugging over USB:

1. Open the device **Settings** panel.
2. Scroll to **About Phone**.
3. Tap on the **Build number** seven times (no, really!).
4. Now go back to the main **Settings** view and you'll see **Developer options**. 
5. Switch on **USB Debugging**. If you are using Visual Studio and don't see the Surface Duo as a deploy target, this is what you've probably forgotten.

## Sad Face :-( I don't have a Surface Duo yet

Don't be too sad, you can still use the Surface Duo emulator. To launch the Surface Duo emulator such that Visual Studio can use it:

1. From the Windows Start menu, select **Surface Duo Emulator for Visual Studio**.

That's it. You should now see it as a deploy target in Visual Studio.


## Review

We've created a new Visual Studio project, and installed the NuGet packages required to use the SkiaSharp game library. At this point, all the code will do is create a blank display, however if just want to download the project at this stage as the basis for your own projects, you'll find it in Files called "DuoGame.zip". Download it, expand it, and load it into Visual Studio. 

Now it's time to draw images to the screen. There are three things we need to worry about: adding the bitmap to the project, loading it into the program at run-time, and finally displaying it. Let's get started.

## Stage 2 - Graphics

Now we've the framework in place, and it's time to load some images and draw them on the screen, because a game without graphics.. well, it's a text adventure. And there's a time and a place for Zork, but it's not right now.

## Drawing to the SkiaSharp Canvas - Adding the bitmap

First let's get an image into the solution.

1. Create a new folder under the **DuoGame** project called **Images**. When it comes to loading images, the case used to name files and folders is important.

2. Find your favorite image in PNG format, and drag it into the Images folder. I'm using a NinjaCat.

3. In the image file's properties, set **Build Action** to **Embedded resouce**. It should look like this:

<img src="https://github.com/GrantMeStrength/Build-a-game-for-the-Surface-Duo/blob/main/resource.png" alt="Adding an image requires setting the Build Action." width="400"/>

## Drawing to the SkiaSharp Canvas - Loading the bitmap

As I suspect you'll want to use more than one bitmap in your game, let's write a method that will load a bitmap.

Add this method to **Mainpage.xaml.cs**:

```csharp
   public SKBitmap LoadBitmap(string resourceID)
        {
            Assembly assembly = GetType().GetTypeInfo().Assembly;
            using (Stream stream = assembly.GetManifestResourceStream(resourceID))
            {
                if (stream != null)
                {
                    return SKBitmap.Decode(stream);
                }
                return null;
            }

        }
```

Once you add this code you'll see some red-squigglies until various keywords, so use the "Show potential fixes" option to add the right **using** statements to the top of the file. Remember you can always download the zipped project files if you get lost.

Create a variable to store the bitmap by adding this line immediately before **public MainPage()**, towards the top of the file:

```csharp
SKBitmap ninjaCatBitmap;
```
Now add this method. It is called just as the view appears, and it's handy place to do the bitmap loading:

```csharp
   protected override void OnAppearing()
   {
        base.OnAppearing();
        ninjaCatBitmap = LoadBitmap("DuoGame.Images.ninjacat.png");
   }
```
You might want to build and run this now, because if something went wrong, it will fail. Badly. Things to check: did you get the casing of the Images folder and file name right? Notice how periods are being used where you might expect slashes. Did you remember to make the image an Embedded Resource?

## Drawing to the SkiaSharp Canvas - Drawing the bitmap

Now we're reading to draw the bitmap.

Replace the existing **OnCanvasViewPaintSurface** method with this code:

```charp
      void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
        {
            SKSurface surface = args.Surface;
            SKCanvas canvas = surface.Canvas;

            // Clear the screen
            canvas.Clear();

            // Draw the bitmap
            canvas.DrawBitmap(ninjaCatBitmap, 200, 200);
        }
```

We've already created the canvas object in the XAML in **MainPage.xaml**, so this method gets a handle to it and then draws our bitmap.

<img src="https://github.com/GrantMeStrength/Build-a-game-for-the-Surface-Duo/blob/main/firstbitmap.png" alt="Is it a bird? Is it a plane?" width="400"/>


Here you can see the bitmap has been drawn. The Surface Duo screen is really sharp with a *lot* of pixels, and so my bitmap looks tiny!

## Review

So far we've created the most boring game in the world, entitled "Look at an image". However, it has proved we can do several important things:

1. Load a bitmap from a resource
2. Draw a bitmap

And of course, since this is a Surface Duo, you can drag the app to the center of the screen and it will expand to fill the entire view. 

You can download the project from the files area, and at this stage it is called "DuoGameBitmap.zip".

## Stage 3 - Animation

It's time to move it, move it!

The simplest way to animate your bitmap is to keep redrawing it in new locations as quickly as possible. It ain't much, but it's honest work.

The following code does just thi, and it should replace **MainPage.xaml.cs** completely. It does several new things:

1. Defines variables to store where the bitmap should be drawn, and the direction it should move.
2. Creates a **Stopwatch** object. This is a way of pausing the program for 1/30th of a second (or even 1/60th).
3. Defines a new method called **AnimationLoop**. This method updates the bitmap co-ordinates, triggers a redraw of the screen, and then uses the **Stopwatch** to pause for a fraction of a second - and then repeats ad nauseum.

That's all it takes to make our bitmap bounce around the screen, as smooth as a buttery bitmap can go.

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Diagnostics;
using System.IO;
using System.Linq;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Xamarin.Forms;

namespace DuoGame
{
    public partial class MainPage : ContentPage
    {
        Stopwatch stopwatch = new Stopwatch();
        float x = 200, y = 200;
        float dx = 11, dy = 7;
        bool pageIsActive = true;
        SKBitmap ninjaCatBitmap;

        public MainPage()
        {
            InitializeComponent();
        }

        protected override void OnAppearing()
        {
            // This method is automatically called when the app appears

            base.OnAppearing();

            // Load the bitmaps
            ninjaCatBitmap = LoadBitmap("DuoGame.Images.ninjacat.png");

            // Start the animation loop
            AnimationLoop();
        }

        void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
        {
            // This is called whenever the screen needs redrawn

            SKSurface surface = args.Surface;
            SKCanvas canvas = surface.Canvas;

            // Clear the screen
            canvas.Clear();

            // Draw the bitmap
            canvas.DrawBitmap(ninjaCatBitmap, x, y);
        }

        async Task AnimationLoop()
        {
            // This just keeps going until the app isn't
            // visible any more.

            while (pageIsActive)
            {
                stopwatch.Start();

                x = x + dx;
                if (x < 0 || x > 1024) dx = -dx;
               
                y = y + dy;
                if (y < 0 || y > 1024) dy = -dy;

                canvasView.InvalidateSurface();
                await Task.Delay(TimeSpan.FromSeconds(1.0 / 30.0));

                stopwatch.Stop();
            }
        }

        private void OnTouch(object sender, SKTouchEventArgs e)
        {
           
        }

        protected override void OnDisappearing()
        {
            // This is called when the app is no longer visible

            base.OnDisappearing();
            pageIsActive = false;
        }

        public SKBitmap LoadBitmap(string resourceID)
        {
            // Load the bitmap given its name

            Assembly assembly = GetType().GetTypeInfo().Assembly;
            using (Stream stream = assembly.GetManifestResourceStream(resourceID))
            {
                if (stream != null)
                {
                    return SKBitmap.Decode(stream);
                }
                return null;
            }

        }
    }
}
 ```
 
## Make the background fancier

If you don't like the murky grey default background, you can create a larger bitmap for the background and then draw that rather than calling the **canvas.Clear()** method - it doesn't seem to incur much of a speed penalty, and it makes all the difference to your game.

We create a second bitmap variable, call the **LoadBitmap** method again (told you it would be useful), and draw it in the **AnimationLoop** code. It already looks a lot better, right?

<img src="https://github.com/GrantMeStrength/Build-a-game-for-the-Surface-Duo/blob/main/city.png" alt="Using a bitmap background to erase the screen" width="400"/>


 
## Review

We've now added the ability to move bitmaps around the screen! 

You can download this final project as **DuoGameAnimated.zip**.

What's next? Well, you'll need to add some form of input and of course sound effects would be nice. And what about using that hinge on the Duo to do something? And working with the expanded screen? Well, if you can't work all that out for yourself, hang tight as I will be back to add more to Building a Game for the Surface Duo..

-john

