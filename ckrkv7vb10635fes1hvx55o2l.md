## Adaptive Icons

Adaptive icons are definitely not a new thing in the Android world. They were introduced in Android 8.0 (Oreo), around four years ago. Not so many things have changed since then, but I think it will be good if we do a quick review on the topic.

## Legacy Icons
Let's talk about how icons were implemented in versions prior to Oreo, which we will call *legacy icons* from now on. Legacy icons are easy to implement. You need to have at least one PNG for each different density from *mdpi* to *xxhdpi* (four in total), but ideally you should also add one for the *xxxhdpi*. The icon's size needs to be 48x48 dp and it can have any transparency. This opens the opportunity to have icons with different types of shapes. It means your icon can be a circle, square, or any shape really.

![Holo launcher](https://cdn.hashnode.com/res/hashnode/image/upload/v1626448223739/Fb2DAGzQb.jpeg)*Source: [androidauthority.com](https://www.androidauthority.com/holo-launcher-hd-app-review-108476/)*

Initially having a unique shape was a good practice because it meant your app icon was more likely going to stand out compared to other apps. But this resulted in a Launcher full of icons with different shapes and sizes and a lot of inconsistency.

## Normalizing icon shapes and sizes
This started a new movement on trying to normalize the icons shapes and sizes in the Android Platform. Samsung was the first OEM that introduced a background behind the icons that make them look more consistent.

![Samsung rounded icons](https://cdn.hashnode.com/res/hashnode/image/upload/v1626448440849/LZRj6bkmL.jpeg)*Left: Standard icon look. Right: Icons with backgrounds. Source: [cnet.com](https://medium.com/r/?url=https%3A%2F%2Fwww.cnet.com%2Ftech%2Fmobile%2Fhow-to-change-to-rounded-icons-on-the-galaxy-s7-s7-edge%2F)*

The second attempt at trying to introduce a more consistent icon experience was in Android 7.1 (Nougat) with the introduction of the `roundIcon`. This new feature had some restrictions, especially with OEMs looking to differentiate their devices by having different icon shapes (ie: Pixel devices use circular icons, Samsung a rounded square shape, other OEMs might use a rectangular shape). This was solved in Android 8.0 with the introduction of Adaptive Icons.

## So… Adaptive Icons
An Adaptive Icon is made up of two layers, a foreground and background, both of 108x108 dp. Ideally these two layers should be vector assets, but they can also be PNGs. The downside of using PNGs is that you will need to provide at least 4 different PNGs per each density (*mdpi* to *xxhdpi*) for each layer (8 PNGs in total). With vector assets you will only need two files which will save you a lot of space.

Basically, Android will apply a mask to both layers and clip an area around your icon. Different OEMs will apply different shapes of masks. You need to have in mind that because of the applied mask you will need to put your icon in a safe area that is around 33dp from the center of the icon.

![Icon mask shapes](https://cdn.hashnode.com/res/hashnode/image/upload/v1626448632096/IZ46WE5dT.gif)*Adaptive icons support a variety of masks which vary from one device to another. Source: [developer.android.com](https://medium.com/r/?url=https%3A%2F%2Fdeveloper.android.com%2Fguide%2Fpractices%2Fui_guidelines%2Ficon_design_adaptive)*

## How to create an Adaptive Icon?
If you are a developer (with no design skills like myself) the short answer is to use the [Asset Studio](https://medium.com/r/?url=https%3A%2F%2Fdeveloper.android.com%2Fstudio%2Fwrite%2Fimage-asset-studio%23create-adaptive) from Android Studio. Using Asset Studio is very easy, you can import both of your layers and the Asset Studio will generate the files for you.

> If your app supports Android < 8.0 you can setup Asset Studio to also generate both the legacy icon and the round icon.

The Asset Studio will show you the safe zone and a really nice preview on how different masks will be applied to your icon. You can resize both of the layers if needed. The background layer can be just a color drawable, but as I mentioned before you can also provide a vector asset, that means the background can be really anything, for example, you can have a gradient as the background layer.

![Asset Studio](https://cdn.hashnode.com/res/hashnode/image/upload/v1626717158178/0M8PQnJ7f.png)

When creating the Adaptive Icon, Asset Studio will generate for you several files. It will add the two layers as drawables, for example, if you used vector assets for your layers it will generate a `drawable/ic_launcher_foreground.xml` for your foreground layer and a `drawable/ic_launcher_background.xml` for your background layer.

The Adaptive Icon file by default will be called `ic_launcher.xml`. If your app's minimum SDK is 26 or higher this file can live in the mipmap resource directory, but if not (most of the apps), it will be stored in the `mipmap-anydpi-v26` resource directory. This is because Adaptive Icons are only supported on API level 26 or higher, and on versions below 26 you will need to have the legacy icon saved in the `mipmap-mdpi`, `mipmap-hdpi`, etc.

For example, if your minimum SDK is 23 you could end up with this file tree in your `res` directory:

![Icons file tree](https://cdn.hashnode.com/res/hashnode/image/upload/v1626449530832/yxrvHg2pC.png)

> If the `ic_launcher_round` adaptive icon is the same one as the normal `ic_launcher`, instead of duplicating the file you can provide a resource alias in your `values-anydpi-v26` folder.
``` xml
<resources>
     <mipmap name="ic_launcher_round">@mipmap/ic_launcher</mipmap>
</resources>
```

Now you only need to specify the icon file in your app's `AndroidManifest.xml` like you would normally do.
``` xml
    <application
        android:icon="@mipmap/ic_launcher"
        android:roundIcon="@mipmap/ic_launcher_round"
        ...
```
And this is how the Adaptive Icon looks internally. It is pretty simple, you only need to use the `adaptive-icon` tag and inside add both layers using the `background` and `foreground` tags (Asset Studio does this for you).
``` xml
<adaptive-icon xmlns:android="http://schemas.android.com/apk/res/android">
    <background android:drawable="@drawable/ic_launcher_background"/>
    <foreground android:drawable="@drawable/ic_launcher_foreground"/>
</adaptive-icon>
```

### For designers 🧑‍🎨
If you are a designer and like to use fancy design tools, you can also use these tools to design Adaptive Icons.
- [Sketch Template](https://github.com/nickbutcher/AdaptiveIconPlayground/tree/master/art) by [Nick Butcher](https://twitter.com/crafty)
- [Adobe Illustrator Template](https://github.com/nickbutcher/AdaptiveIconPlayground/tree/master/art) by [Nick Butcher](https://twitter.com/crafty)
- [Adobe XD Template](https://github.com/faizmalkani/adaptive-icon-template-xd) by [Faiz Malkani](https://twitter.com/malkani_faiz)
- [Bjango Templates](https://github.com/bjango/Bjango-Templates) by [bjango](https://bjango.com/)

## More Adaptive Icon stuff...
The real reason for having two layers is because it allows OEMs to do really cool animations when an user interacts with your icon, like moving the foreground layer on top of the background layer.

![Icon Parallax Demo](https://cdn.hashnode.com/res/hashnode/image/upload/v1626455660048/HjIQxni3l.gif)
*Adaptive icons support a variety of visual effects. Source: [developer.android.com](https://medium.com/r/?url=https%3A%2F%2Fdeveloper.android.com%2Fguide%2Fpractices%2Fui_guidelines%2Ficon_design_adaptive)*

But, how can we test the animated visual effects of our Adaptive Icons in a real device? Well, thanks to [Nick Butcher](https://twitter.com/crafty) again, we can use his [Adaptive Icon Playground](https://github.com/nickbutcher/AdaptiveIconPlayground) app to see how the animated visual effects perform in the icon. You can download the APK from the repo, run the app and you will be able to see the Adaptive Icons of all of your installed apps in your device.

![Adaptive Icon Playground](https://cdn.hashnode.com/res/hashnode/image/upload/v1627239109499/ugvje8QF6.gif)

## Wrapping up
Implementing Adaptive Icons is a great way to make your app follow the best practices and is in tune with the other apps on the devices of our users. Remember that App Icons are a great investment because they usually are the first thing the user sees, and that also involves seeing the animated visual effects in the latest Android versions or supporting different Icon shapes in different OEMs.

>At the time of writing this article, Android 12 is in beta, but hopefully when it gets stable in the next month I'll update the article if we get something new.

## Resources
%[https://developer.android.com/guide/practices/ui_guidelines/icon_design_adaptive]
%[https://medium.com/google-design/understanding-android-adaptive-icons-cee8a9de93e2]
%[https://medium.com/google-design/designing-adaptive-icons-515af294c783]
%[https://medium.com/androiddevelopers/implementing-adaptive-icons-1e4d1795470e]
%[https://developer.android.com/studio/write/image-asset-studio#create-adaptive]

*Special thanks to [Chris Weathers](https://twitter.com/clw), [Camilo](https://twitter.com/Terminaitor31) and [Mauricio](https://twitter.com/itolisto) for reviewing this.*



 

