<h1>A Quantitative Study of the Extent to Which Region-of-Interest Can Be Used for Viewpoint Motion Prediction</h1>

This repository contains the code and figures used to undertake research for my final year project at the University of York. This project was completed in partnership with AMD.

This project aims to develop a predictive model utilising frame data to predict
viewpoint motion in video games to enable pre-rendering and delivery of
frames in a cloud gaming scenario. A further aim is to use the predictive
model to reduce encode time by indicating which pixels are likely to remain
in the frame.

Two predictive models were developed, one using image data and one
without to predict viewpoint motion. These were trained on a dataset of
speedruns of the videogame Doom. Additionally, a method to use the
viewpoint motion predictions to assist the video encoder was developed.

The results show that image models have a huge potential to reduce latency
in cloud gaming systems over current methods. They also demonstrate
that predictive models in cloud gaming can also be used to lower data
transmission as an added benefit of implementing predictive rendering.

<br>

<h2>Baseline Model</h2>

A model trained only using previous motion vectors was created as a baseline and for comparison, where Pt
is the motion vector at the point of prediction and M is the
size of the input window, and H is the size of the prediction window. This
means that the model takes in the M motion vectors previous to the frame,
and outputs H motion vector predictions.
The model was based on LSTMs and uses a seq2seq architecture. A
seq2seq architecture consists of an encoder and a decoder. The M input
motion vectors are input to the encoder, which generates and maintains
a representation that is passed to the decoder, which predicts H motion
vector predictions, which it does by making one prediction at a time that is
fed back as input for the next prediction.

<h2>Multimodal Model</h2>

The main model takes 3 types of input: previous motion vector, semantic
segmentation map, and depth map.

The seq2seq approach is taken again, with the encoder taking all 3 types of
input, and the decoder making predictions using the hidden state, and the
predicted motion vector for the frame before. This is represented in Figure
3.3, where St
is the semantic segmentation map at the point of prediction
and Dt
is the depth map.

<b>Encoder</b>
<ul>
<li><b>Content LSTM</b> - The semantic segmentation map and depth map are
processed by convolution layers before being concatenated and passed into
the content LSTM, which has a hidden size of 256. This LSTM maintains a
hidden state representing the content of the frames in the input window.</li>

<li><b>Inertia LSTM</b> - The previous motion data is processed by a fully connected
layer before being passed to the inertia LSTM, with a hidden size of 256.
This LSTM maintains a hidden state representing the movement of the
player over the input window.</li>

<li><b>Fusion LSTM</b> - The output of the content and inertia LSTMs are concatenated and input into the Fusion LSTM, which has a hidden size of 256. The
goal of this LSTM is to fuse the outputs of the 2 LSTMs in a helpful way.</li>
</ul>
<b>Decoder</b>

The decoder consists of the same components as the encoder but with
different weights. Each of the components receives the hidden state from
the encoder of the corresponding component. Here, however, the visual
LSTM receives a masked input reflecting the lack of knowledge of the next
frame. The output of the decoder is re-injected as the motion vector input
for the next prediction.

<h2>Encoding</h2>
Viewpoint motion predictions, as output from the predictive models above,
can be used to determine which pixels are likely to remain in a frame. This
can be used to assist the video encoder by indicating which regions should
be compressed less.
A method to derive a heatmap to assist the encoder utilising the motion
vector predictions was developed.
