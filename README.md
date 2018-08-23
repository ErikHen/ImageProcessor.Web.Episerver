# ImageProcessor.Web.Episerver

This package will install the following ImageProcessor extensions:
  - IImageService implementation to read images from Episerver
  - IImageCache implementation that caches images in the configured Episerver Blob storage (the cache is self healing and cleaning) 
  - ImageProcessingModule to render processed images in edit mode.

And on top of that you also get a, strongly typed, fluent API to use in your views

## Installation
- Install this package from the Episerver NuGet feed (http://nuget.episerver.com/feed/packages.svc/). All dependencies and necessary configuration changes will be made for you.


## Render Image in Markup
Most convenient way to render image in markup would be use `HtmlHelper` extension method:

```
@using ImageProcessor.Web.Episerver

<img src="@Html.ProcessImage(Model.CurrentPage.MainImage).Resize(100,100)" />
```

This will make sure that markup for visitors would be (assuming that image is `png`):

```
<img src="/.../image.png?width=100&height=100">
```

And in edit mode it would generate something like this:

```
<img src="/.../image.png,,{CONTENT-ID}?epieditmode=False&width=100&height=100">
```

`ProcessImage` returns back `UrlBuilder` type, so you can fluently chain any additional paramters if needed:

```
<img src="@Html.ProcessImage(Model.CurrentPage.MainImage).Resize(100, 150).BackgroundColor("red)" />

```
Alternatively you could supply the image with all the parameters in the HTML:
```
<img src="@Url.ContentUrl(Model.Image)?width=75" />
```
See http://imageprocessor.org/imageprocessor-web/imageprocessingmodule/ for all options

## Picture Helper
This package also include an Html helper that renders a [Picture element](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/picture) that let's you have responsive, optimized, and lazy loaded images.

Example usage
```
@Html.Picture(Url.ContentUrl(Model.Image), ImageTypes.Teaser, lazyLoadType: LazyLoadType.Progressive)
```
#### Parameters
* **imageUrl (string or UrlBuilder)** <br/> 
* **imageType (ImageProcessor.Web.Episerver.ImageType)** <br/> An "Image type" defines the possible sizes and quality for an image. <br/> [Example of how to define image types](https://github.com/vnbaaij/ImageProcessor.Web.Episerver/blob/master/samples/AlloySampleLocal/Business/Rendering/ImageTypes.cs)
* **cssClass (string)** <br/> Will be added to the rendered img element.
* **layzLoadType (ImageProcessor.Web.Episerver.LazyLoadType)** <br/> 
When lazy load type is "Regular", the srcset attribute of the source element (inside the rendered picture element) will be empty, 
and an additional attribute (data-srcset) will be added that contains the image url(s). 
That enables you to lazy load the image after the rest of your page content is loaded. <br/>
When lazy load type is "Progressive", the srcset attribute will contain image url(s) for a low quality version of the image, and makes it possible to lazy load the high quality image.<br/>
[Javascript example of how to lazy load the images](https://github.com/vnbaaij/ImageProcessor.Web.Episerver/blob/master/samples/AlloySampleLocal/Static/js/lazyImages.js)

The picture helper is described in more detail [here](https://hacksbyme.net/2018/03/19/picture-element-imageprocessor-responsive-images-made-easy/) and [here](https://hacksbyme.net/2018/05/12/optimize-your-images-with-imageprocessor/)
