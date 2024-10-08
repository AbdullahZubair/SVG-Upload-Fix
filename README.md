When I first received the requirement to support SVG files on my Drupal 7 site, I was excited to explore my options. After some research, I decided to use the [SVG Image module](https://www.drupal.org/project/svg_image), which seemed simple and aligned perfectly with my needs. However, I was taken aback when I enabled SVG support for an image field. Upon attempting to upload an SVG file through the node creation form, I encountered the error: 

*“The specified file 'default.svg' could not be uploaded. The image file is invalid or the image type is not allowed.”*

To make matters worse, when I tried to set an SVG as the default image, I received another frustrating error: 

*“'default.svg' could not be uploaded. Only files with the following extensions are allowed: jpg, jpeg, gif, png, txt, doc, xls, pdf, ppt, pps, odt, ods, odp.”*

Determined to find a solution, I dove into the issue and came across an ongoing discussion in [Issue #3458204](https://www.drupal.org/project/issues/svg_image/3458204). It became evident that I wasn't alone in facing this challenge. While a patch had been provided by a dear friend, [Sam](https://www.drupal.org/u/isalmanhaider), I was hesitant to modify the core functionality of Drupal. Though I tried the patch and successfully resolved the node creation error, the default image issue persisted.

After digging deeper, I realized that when uploading files through the node creation form, Drupal attempts to validate the image type. Since raster and vector images are fundamentally different, SVG files were not being validated correctly, leading to the errors I was encountering.

To address this, I implemented a condition that checks if the field type is an image and whether SVG is included in the allowed file extensions. If an SVG is detected among the uploaded files, I modified the validation process to skip the standard image validation for that specific SVG file. This way, raster images would still be validated normally, while SVG files could be uploaded seamlessly without any hassle.

With the first issue resolved, I turned my attention to the second error: the inability to set an SVG as a default image. I realized that the default image upload process was not recognizing SVGs due to its reliance on the core allowed extensions, defined in `includes/file.inc`. Again, I was reluctant to modify the core, as I wanted to maintain best practices in my development.

Instead, I altered the field edit form, applying similar conditions to allow SVG as a valid extension for default images. With this change, SVG files could be uploaded successfully as default images as well.

During this process, I discovered another important detail about default images. Each image field has two instances: a default instance that is applied site-wide and a field-specific instance that overwrites the default for that particular field. To accommodate this, I ensured my solution worked for both scenarios, allowing for SVG uploads in both instances.

Finally, I wanted to customize the upload path for default images. While I could easily set custom paths for user-uploaded images, the default images were restricted to the `default_images` directory within the public files directory, specifically `public://default_images/`. To improve this, I added a feature that checks if a specific file directory is set for the field being edited. If so, the module would place the default image in that directory, ensuring better organization for uploaded SVG files.

I’m sharing this module publicly to help others who might encounter the same issues I faced. It took a considerable amount of time for research and debugging, but I’m glad to have created a solution that can benefit others in the Drupal community. Thank you!
