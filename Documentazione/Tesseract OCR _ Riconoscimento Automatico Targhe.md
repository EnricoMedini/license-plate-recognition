# Riconoscimento Targhe

## Tesseract Ocr

Tesseract is an optical character recognition engine for various operating systems. It can be used for recognising characters or strings of characters from a variety of image file formats, and outputting them to a variety of text file formats.

###### Links

- Git: https://github.com/tesseract-ocr/tesseract
- Documentation and User Manual: https://tesseract-ocr.github.io/

* * *

#### CLI Tool

It's available as a command line tool, with various options.

CLI Syntax:

`tesseract input_image_file output_text_file --options`

* * *

#### `tesserocr` Python Wrapper

It's available as a Python Module, PIP-installable. This module integrates with Tesseract C++ API using Cython.

###### Links

- https://github.com/sirfz/tesserocr

* * *

#### `pytesseract` Python Wrapper

It's available as a Python Module, PIP-installable. This module needs the Tesseract executable.

`pip install pytesseract`

###### Links

- https://pypi.org/project/pytesseract/

* * *

#### Considerations

Based on a series of tries, the options need to be correctly set to have the program recognise and output anything at all.

Trying a number of options, on a rectified image of only a license plate, still gave an output text full of errors.

Tesseract OCR seems to use by default a "neural nets LSTM" mode. The program probably needs to train a neural net on recognising license plates to achieve dependable and satisfying results.

Accuracy of Tesseract OCR, even with manually rectified images with a color depth of 1 bit, is incredibly low and unreliable. A trained neural net is required.

To further improve Tesseract OCR reliability, the combined use (permitted by Tesseract itself) of Tesseract's own legacy engine and the neural net LSTM could be studied. Tesseract's legacy engine will need a set of data, linked under **Links** section. LSTM will also need to be trained before testing the combined use. Two set of data for LSTM are also available to download under the **Links** section, but they are not specialized datasets, so training the neural net on license plates is advised.

###### Links

- LSTM datasets:
    https://github.com/tesseract-ocr/tessdata_best
    https://github.com/tesseract-ocr/tessdata_fast
- Legacy dataset:
    https://github.com/tesseract-ocr/tessdata/releases

* * *

## OpenCV

OpenCV (Open Source Computer Vision Library) is a library of programming functions mainly aimed at real-time computer vision. It can be used to recognise pattern or shapes in an image. It can also be used for image manipulation and pre-processing (cutting a shape, rectification, turning to monochromatic).

###### Links

- https://opencv.org/
- https://github.com/opencv

* * *

### Steps for a possibile Python Script

- Recognising the shape of the license plate in the photo
- Cutting the shape out and creating a new image with only the result
- Rectifying the image
- Turning the color depth of the image to 1 bit
- Analyising the new, optimised image of the license plate to recognise the characters (alphanumeric characters only)
- Outputting the result on a text file

### Steps for an optimised Python script

There are two hard requirements to achieve precise and correct results:
**Tesseract OCR will need a trained dataset of Italian license plates for the LSTM engine, and a downloadable dataset for the legacy engine.
The images will need to be heavily pre-processed using OpenCV's functions.**

There are two possible ways to make the script.

The first one:

- Recognising the shape of the license plate in the photo
- Cutting out the shape and generating a new image from it
- Rectyifing (*deskewing*) the image
- Rotating the image if needed
- Grayscale
- Denoise
- Treshold
- Erasing the leftover contour in the image
- Configuring Tesseract with a whitelist of characters to recognize
- Feeding the pre-processed image to Tesseract OCR, selecting the best options available (those will probably need to be texted one by one)

This way will probably provide the most accurate and reliable results, even if it will be longer to script out.

The second way omits cutting out the license plate shape, and rely on heavily pre-processing the image only. This one will probably be harder to test and optimize, and will provide worse results.

###### Links

- In-Depth Guide: https://nanonets.com/blog/ocr-with-tesseract/#preprocessingfortesseract
- Git of a complete and optimized program: https://github.com/siddharth7997/License-Plate-Recognition
- Tutorial with completed script to test accuracy of the program: https://www.geeksforgeeks.org/license-plate-recognition-with-opencv-and-tesseract-ocr/
- Another guide: https://yashlahoti.medium.com/number-plate-recognition-in-python-using-tesseract-ocr-cc15853aca36
- Git of a complete program: https://github.com/anmesh-vicky/Number-Plate-Recognition

* * *

## Various Tries

#### First one

Tried Tesseract-OCR 4.1.1 CLI On Debian 11.

- Gave an input image of a slightly skewed license plate on a car, easily readable.
- Gave no options to the program.
- Output text was completely empty.

#### Second One

- Gave a prospective-corrected and cut image of the license plate only, easily readable.
- Gave no options to the program.
- Output text was completely empty.

#### Third One

- Gave a perspective-corrected and cut image of the license plate only, easily readable.
- Gave the option `--psm 6` to the program.
- Output text consisted of a string of characters. The number of characters was correct, all but two characters were completely wrong.

#### Fourth One

- Gave a perspective-corrected and cut image of the license plate only, easily readable.
- Gave the options `--psm 6 --oem 1` to the program.
- Output text consisted of a string of characters. The number of characters was correct, all but two characters were completely wrong.

#### Fifth One

- Gave a perspective-corrected and cut image with a color depth of 1 bit, of the license plate only, easily readable.
- Gave the options `--psm 6 --oem 1` to the program.
- Output text consisted of a string of characters. The number of characters was correct, all but two characters were completely wrong. The string was different from before, but still wrong.

#### Siddhart7997's Script try

- Launched from a terminal the script
- After many tries, the script always interrupts with an error that seems to be linked to OpenCV. It seems that the script can't find the loaded image.
- Error Message `cv2.error: OpenCV(4.5.4-dev) /tmp/pip-req-build-iefu5nf2/opencv/modules/highgui/src/window.cpp:1006: error: (-215:Assertion failed) size.width>0 && size.height>0 in function 'imshow'`