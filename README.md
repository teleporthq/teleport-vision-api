![Alt text](https://raw.githubusercontent.com/teleporthq/teleport-lib-js/master/logo50.png "TeleportHQ")

## Vision API
![alt text](https://i.imgur.com/K3ut2Dv.jpg "teleportHQ Vision API")
**teleportHQ Vision API** is a computer vision API specifically trained for **detecting atomic UI elements in pictures** of hand-drawn wireframes (as seen in the picture above). It uses an architecture based on [Resnet101](https://arxiv.org/abs/1512.03385) for extracting features and [Faster R-CNN](https://arxiv.org/abs/1506.01497) for bounding-box proposals. 

The machine learning model was built and trained using [TensorFlow](https://github.com/tensorflow/tensorflow).

List of elements it can distinguish: `paragraph, label, header, button, checkbox, radiobutto, rating, toggle, dropdown, listbox, textarea, textinput, datepicker, stepperinput, slider, progressbar, image, video`.

The API is currently in **closed alpha**, but feel free to [contact us](#how-do-i-get-a-teleport-token) if you want early access.

## Guideline
We had to decide on some conventions to obtain better results, you can learn more [in this blog post](https://teleporthq.io/blog/enforcing-convention-for-wireframe-object-detection/).

![alt text](https://teleporthq.io/static/blog/enforcing-convention/scan.jpg "Vision API guidelines")


## Using the Vision API
### Request
Send all requests to the API endpoint: `https://api.vision.teleporthq.io/v2/detection`
#### Request header
Make sure to add a `Content-Type` key with the value `application/json` and a `Teleport-Token` key with the key provided by us.
#### Request body
The body of the request is a json with two keys: `image` and `threshold`.
* `image` is a **required** string parameter that denotes the direct url to a publicly available jpg or png image.
* `threshold` is an *optional* parameter. **Default** value is `0.1`. The detection model outputs a confidence score for each detection (between 0 and 1) and won't include in the response detections with confidence lower than this threshold.

Request body example:

<img src="https://i.imgur.com/HzTWzLS.jpg" width="300" height="300">

```
{
    "image": "https://i.imgur.com/HzTWzLS.jpg", 
    "threshold": 0.5
}
```
#### Request example
```
curl \
  -X POST https://api.vision.teleporthq.io/v2/detection \
  -H 'Content-Type: application/json' \
  -H 'Teleport-Token: your_token' \
  -d '{ 
    "image": "https://i.imgur.com/HzTWzLS.jpg",
    "threshold": 0.5 
  }'
```

### Response
If your request is a valid one, you will recieve back a json with the following structure:

```
[
    {
        "box": [y, x, height, width],
        "detectionClass": numeric_label,
        "detectionString": string_label,
        "score": confidence_rating
    },
    ...
]
```
The json contains a list of objects, each one of this objects corresponding to a detected atomic UI element in the image sent in the request. All of the keys will appear in all of the objects in your response array.
* `box` contains the coordinates of the bounding box surrounding the detected element. `x` and `y` are the coordinates of the top left corner of the box and `width` and `height` are self explanatory. All coordinates are **normalized between [0, 1]** where `(0,0)` is the top left corner of your image and `(1, 1)` is the bottom right corner. In other words, if you want to get the pixel coordinates you have to multiply `x` and `width` with the width of your image and `y` and `height` with the height of your image.
* `detectionClass` is the numeric class of the detection.
* `detectionString` is the human-readable label of the detection.
* `score` represents how confident the algorithm is that the predicted object is a correct / valid one. It takes values between `[0, 1]`, where `1` represents a 100% confidence in its detection.

The `detectionClass` to `detectionString` mapping is done according to this dictionary:
```
{
    1: "paragraph",
    2: "label",
    3: "header",
    4: "button",
    5: "image",
    6: "linebreak",
    7: "container",
    8: "link",
    9: "textinput",
    10: "dropdown
    11: "checkbox",
    12: "radiobutton",
    13: "ratting",
    14: "toggle",
    15: "textarea",
    16: "datepicker",
    17: "stepperinput",
    18: "slider",
    19: "video",
    20: "table",
    21: "list"
}
```

#### Response example
Full response [here](https://gist.github.com/DimitriF/986de27fdc5c849696719b8543ca8d35).
```
[
    {
        "box": [
            0.06640399247407913,
            0.18573421239852905,
            0.0626835897564888,
            0.43779563903808594
        ],
        "detectionClass": 15,
        "detectionString": "header",
        "score": 0.995826005935669
    },
    {
        "box": [
            0.16810636222362518,
            0.18520960211753845,
            0.04797615110874176,
            0.17563629150390625
        ],
        "detectionClass": 16,
        "detectionString": "button",
        "score": 0.9924671053886414
    },
    {
        "box": [
            0.8350381255149841,
            0.5098391771316528,
            0.05998152494430542,
            0.23138082027435303
        ],
        "detectionClass": 16,
        "detectionString": "button",
        "score": 0.9921296238899231
    }
]
```

## Previous version

The previous version of the API is still available at this end point:
`https://api.vision.teleporthq.io/v1/detection`

The `detectionClass` to `detectionString` mapping for this previous version is done according to this dictionary:
```
{
    1: "paragraph",
    2: "label",
    3: "header",
    4: "button",
    5: "checkbox",
    6: "radiobutton",
    7: "rating",
    8: "toggle",
    9: "dropdown",
    10: "listbox",
    11: "textarea",
    12: "textinput",
    13: "datepicker",
    14: "stepperinput",
    15: "slider",
    16: "progressbar",
    17: "image",
    18: "video"
}
```

## How do I get a Teleport-Token?
If you are interested in using this API, feel free to get in touch with us via the following [form](https://docs.google.com/forms/d/e/1FAIpQLSdLzIL3pnsXZeJW2-S9c064K-I-M-Q-mhpfxuLrXzJOuuRrkw/viewform). 
