# webp-realizer
With this solution, you are able to write ie:
```
<picture>
  <source srcset="image.jpg.webp" type="image/webp" />
  <img src="image.jpg" />
</picture>
```
&ndash; and have "image.jpg.webp" automatically generated for you the first time it is requested.

It works by .htaccess magic coupled with an image converter. Basically, a rule in the .htaccess detects when a file ending  ".jpg.webp", ".jpeg.webp" or ".png.webp" is requested, but does not exists. When this happens, it routes the request to the converter, which looks for an image with the same extension minus ".webp", converts it, saves it and serves it. On subsequent requests, the webp file is there, and simply served.

The image converter is able to use several methods to convert the image (imagick extension, gd extension, directly call cwebp binary or connect to ewww image optimizer cloud service). To learn the options, go to the [project on github](https://github.com/rosell-dk/webp-convert)

## Installation
To use the solution on your website, copy the two .htaccess and the webp-convert directory into your root folder. Also make sure that www-data has permission to write in the relevant portion of your site.


## Configuration
The quality and the location of the converted files is customizable. 

For location, you have three options:

1. A converted file resides in the same folder as the original. The file will get the same name as the original plus ".webp". This corresponds to the following usage:
```
<picture>
  <source srcset="images/button.jpg.webp" type="image/webp" />
  <img src="images/button.jpg" />
</picture>
```

2. Converted files resides in a specific folder. The converted files are organized into the same structure as the original. This corresponds to the following usage:
```
<picture>
  <source srcset="webp-cache/images/subfolder/logo2.jpg.webp" type="image/webp" />
  <img src="images/subfolder/logo2.jpg">
</picture>
```

You choose between the three options by commenting/uncommenting blocks in the .htaccess. Precise instructions are given in the .htaccess.

## Verifying that it works
Simply check that an image displays both in a browser with WebP support (ie Google Chrome or Opera) and in a browser without (ie Firefox or Internet explorer). Also check that the converted file is created.

## Limitations
* The solution does not work on Microsoft IIS server

## FAQ

## Roadmap

* Updating the original image will not cause the converted image to be updated. It would be possible though: The converted image could have the modification date of the original as part of the filename. Of course, this will only make practical sense when the markup is generated automatically.
 
## A similar project
The project is very similar to [WebP on demand](https://github.com/rosell-dk/webp-on-demand). The converter is the same, but the approach is somewhat opposite. Instead of rewriting requests for non-existing webp-files, it redirects requests for existing jpeg/png files - but only for clients that supports WebP.

Pros and cons of *WebP realizer* compared to [Webp on demand](https://github.com/rosell-dk/webp-on-demand):

PRO: WebP realizer requires no special setup on CDN's<br>
CON: WebP realizer does not work on images referenced in CSS<br>
CON: WebP realizer requires you to add extra HTML markup (manually or by other means)<br>


## Wordpress adaptation
I'm planning to make a Wordpress adaptation, which automatically creates the markup.

One solution is to work on entire output with output buffering. But the downside of doing that is that it slows things down, because content isn't allowed to be sent to the browser as its ready, but instead has to wait for the full document to be rendered (more about the approach [here](https://stackoverflow.com/questions/772510/wordpress-filter-to-modify-final-html-output)). 

Another solution is to find relevants hook. *get_image_tag()* looks very promising (but I don't know if plugins that inserts images generally uses the hook. If not, then there will be additional work getting it to work with selected popular plugins, such as galleries)

A javascript based solution seems out of the question, because in order to work with the DOM, one must wait for the DOMContentLoaded event. But by the time that event triggers, the browser has already started loading images (only tested in one browser, but I believe it is standard now-a-days)

