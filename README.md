# Portrait Photo Generator

In this repo we are going to use image segmentation model(facebook/detr-resnet-50-panoptic) to generate portrait photo of any object present in a photo. Then we are going to make a deployable app using gradio and finally, we will host it in hugging face hub spaces.

## User Interface
Here is a screenshot of the interface. The app has been hosted in hugging face spaces. [Click Here](https://huggingface.co/spaces/rkoushikroy2/portrait_photo_generator) to explore the deployed version.

---

![Interface Snapshot](interface_snapshot.png)
> You can drag and drop images in the top left image box. After you drop an image the image segmentaion model will process the image and provide a list of objects in the drowpdown menu in the right. Now, you can choose any object from the list and that objet will be foucsed in the Output image box. You can adjust the slider to adject the strength of the background blur. The whole interface is reactive and anything you change will trigger an auto refresh. You may find the example images in lower right useful.

## Protrait Image Generation Steps:
- Load segmentation model, pass your image, generate the masks and labels for each object present in the image.
- Make a list of objects from the "label" keys in the prediction dictionaries.
- Add a number identifier so that each object name can be unique. This is important for selecting a object.
- Image segmentation and background blurring method for selected object in the image:
    - Take the mask provided by the segmentation model in the earlier stage.
    - Divide by 255 to make the range 0 to 1.
    - At this point the mask is 1 channel. Make it three channel by coping the single channel three times.
    - Element wise multiply the input image and the three channel mask. 
    This will give an image where only the segmented part of the image will be present. This means, only the selected object pixels will be intact and other pixels will be black (0).
    - Now, take the original image and blur it using any kind of blurring kernel. Here I used Gaussian Blur.
    - After that, create a invert of the three channel mask created the previous steps.
    - Element wise multiply the invert mask and blurred image. 
    This will give an image where the blurred background of the selected object will be present and 
    the pixels associate with the object of interest will be blank/black/value=0.
    - Then, add up the segmented image and reverse segmented (blurred background portion). 
    This will give the desired portrait photo looking output.
    - Smoothen the image.

---

## Gradio GUI Steps:
- Used block element for more control over the interface.
- Used image.change, slider.change and dropdown.change event handler for generating output each time any of these changes.

---

## Requirements
```
gradio
numpy
Pillow
torch
timm
transformers
```
```
pip install -r requirements.txt
```

## Run App
```
python app.py
```
---

## Files
| File | Contents |
|---|--- |
| [Segmentation Notebook](segmentation.ipynb) | Here the segmentation model to background blur part has been shown step by step.|
| [Gradio Notebook](gradio.ipynb) | You may experiment with different componets of a gradio app.|
| [Gradio App](app.py) | It is the main file of the deploed version of the portrait photo generator app.|



