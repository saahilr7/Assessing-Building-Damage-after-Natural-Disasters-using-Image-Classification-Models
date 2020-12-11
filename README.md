# Assessing-Building-Damage-after-Natural-Disasters-using-Image-Classification-Models

The project focus is in building an image classification model that evaluates and determines the level of damage that buildings suffer after being affected by natural disasters. Significant progress can be achieved by having a model that can accurately identify the damaged buildings in which those metrics can be aggregated to assess the overall destruction caused by a natural catastrophe. By analyzing satellite imagery and training the model to identify the damage in the image, we plan to build a solution that can provide real-time data of the effect natural disasters have in studied areas and cities.

## THE DATA

Obtaining satellite images was our first step. This dataset had the following characteristics:
* 5,598 high resolution satellite images of geographic areas labeled by the type and name of natural disaster
*	2,799 images before the natural disaster and 2,799 images after the catastrophe.
*	Multiple buildings per image, each building is labeled damaged or not damaged in a separate file that references the building ID.
*	Requires significant preprocessing to identify the buildings in each image according to the coordinates provided. For this we decided as part of the preprocessing a polygon figure would extract each building of the image.


## OBJECTIVES

The following objectives were determined for the solution being built:
*	Significantly reduce the time and effort evaluating damage to structures after the catastrophe.
*	Provide a safer way to inspect and determine the damage rather than driving around the area.
*	Calculate more accurate estimates of the financial impact or loss caused by the catastrophe.
*	Better assess the situation or status of a geographic zone after the catastrophe to allocate resources accordingly

## PRE-PROCESSING

The data were organized into pre-disaster and post-disaster portable network graphic images with labels and metadata for each image captured in json files. The json files contained corner coordinates in decimal degrees notation for polygons encompassing each building on each image. 

The baseline model we used as our guide and inspiration, preprocessed only post-natural disaster raw images into image chips of just the buildings and a csv file with the building unique identifier and damage label (target variable). All data, including coordinates for the building corners, damage label, unique building identifier, as well as relevant metadata were captured in json files associated with each raw satellite image. 

We utilized the baseline pre-processing to run both the baseline model from github as well as some adaptations to the model. Achieving mostly unimpressive results, we determined to utilize the pre-disaster images in a model with more of a comparison approach. This required a fundamental change in the preprocessing functions that would adequately parse, label, and name both pre and post disaster images and organize the image chips so they could be pulled into separate image generators based on pre and post disaster. We customized part of the baseline preprocessing and incorporated our own code to accomplish this.

Additionally, we used a baseline pre-processing function that made masks for each building utilizing the polygon dimensions that we had for each building with the intention of removing “background noise” in the image and enhancing the pixels that contained only the buildings. But when we ran our models using this masked version of the buildings instead of the unmasked images, the results were disappointing as we got quite a low F1 score from that analysis. We decided to discard the use of masked versions of the building images due to this and used only the unmasked chipped building images for training our models.

Once the pre and post images were parsed, we further transformed the image chips by applying random transforms (data augmentation) to the training set with regard to vertical/horizontal shifts, height/width shifts, shear and zoom range, etc, and mean normalized each image chip. Also, the no damage category was significantly over-represented, not for what would likely be seen in a natural disaster, but with respect to equal weighting of the categories from the model’s perspective. As no damage and destruction are perhaps the easiest categories to predict, we applied weights to the labels in an effort to rectify the imbalanced representation. 

## RESULTS

We tried different approaches using our main data set. The first one, our baseline model, was an adaptation of the model we used in our proof-of-concept. The major difference was that, unlike the first dataset, the xView2 data requires a multiclass approach, which also makes the task more complex. Also, as the first model was not prepared to use pre and post images from the same building, we used only “post-disaster” images. Although it may seem strange to use only post-disaster images, it is noteworthy that most buildings are still classified as “no-damage”, and so all four classes are well represented in this chunk of the dataset.

We made use of a pre-trained version of the ResNet50 model, that trained on more than a million images from the ImageNet database, and we concatenated these weights with our CNN model. This set up resulted in a F1 score of 0.49 in the training set, but with a score of almost zero when using the test set.

Our next approach was to adapt a model proposed by Ritwik Gupta in the xView2 GitHub repository. This also a simple CNN network that uses 3 convolution and 3 Max Pooling alternated layers, and later merges the results of the CNN with weights provided by the ResNet50 model, and then, this new input goes through 3 dense layers, before reaching the final layer with 4 neurons. In our project, we had to reduce the number of neurons of the dense layers due to computational constraints, but even though, the results were superior to our baseline model. The F1 score on the train set was 0.74, while on the test set it reached 0.62.

Finally, our last model had a more complex setup as it uses a combination of pre- and post-disaster images. We used what is called a Siamese model, in which the pre-disaster image is passed as an input to a CNN network, while the post-disaster images also are passed as inputs to another CNN network with identical characteristics, but without any interactions between these branches of CNN. Both CNN outputs were later flattened and combined (by subtraction). This new set was then merged with weights from a ResNet50 model and passed as input to a set of 3 dense layers and finally to an output layer with 4 neurons.

## CONCLUSION

The accurate and timely analysis of the post-disaster images will go a long way in helping  reduce the response time for disaster recovery as well as facilitate more efficient disbursement of Federal aid and insurance benefits to affected people. There is also potential for this model to be used in a predictive nature; modeling damage to similar geographic regions based results from past events. The progress we have made here is the first of several steps yet to come in a more complex application of machine learning to disaster response.

For comparison of the various models built, the F1 score parameter was used for evaluating the different models. The best model for the proof of concept model has a F1 score of 0.99 on the training and 0.97 on the test set, which far exceeded our expectations. For the main dataset, we tried three models, the baseline model, Convolution Neural Network (CNN) model, and a Siamese model. The F1 score for the best case architecture is given by the CNN model which is 0.74 on the training data set and 0.62 on the test data set. 
While the computational complexity of this problem set posed some limitations on the extent to which we are able to experiment with model architecture, this dataset did present a good opportunity to refine image classification skills with a real-world application and benefit.  In expanding upon the baseline code found in the referenced Github repository, we were able to establish a good starting point for modifying model architecture and tuning hyperparameters. In building upon this model we ventured into more basic CNN architectures believing that perhaps a simpler model run for more epochs may produce better results; which to a degree it did. However, it also became obvious to us that a model that better replicates the manual damage assessment process would likely yield even better results. When imagery analysts perform damage assessment, a comparison is made between pre-disaster images and post-disaster. The Siamese model provided this type of ability and yielded the most promising results we had seen in our experiments. 

While model run times did not permit us to further refine the Siamese model, we believe that this is the future of this project and intend to expand upon this approach. As the data is quite nuanced in detecting damage levels, it is likely that much deeper models with ability to fully capture the representations within the images are necessary for increased accuracy. 

## LONG TERM VISION AND NEXT STEPS

Over the next few months we plan to continue dedicating time and working to improve the main model performance as well as adding to the different levels of complexity to the model. We plan to keep trying to incorporate different model architectures, image processing techniques, and image manipulation techniques. We will also explore the concept of utilizing models tuned to classify only images of one specific natural disaster (floods, fire, etc). Having one model per type of natural disaster may improve the performance, as each model would be able to identify the specific signs of damage for each type of natural disaster.

We also have interesting features in my mind that we plan to add to the model such as object detection that identifies “HELP” signs in the images from people that have no way to communicate and are facing post natural disaster emergencies. Other possible avenues for future development would be area accessibility rating for lines of communication (roads, bridges, waterways, etc) and change detection for monitoring changing conditions (receding flood levels, area of fires under control, etc) post natural disaster.

## REFERENCES

The following sources were used as references during the project:
*	Tiseo, P., & 5, N. (2020, November 05). Number of natural disasters worldwide 2019. Retrieved November 15, 2020, from https://www.statista.com/statistics/510959/number-of-natural-disasters-events-globally/
*	Main data set:XView2. (n.d.). Retrieved November 16, 2020, from https://xview2.org/
*	Github for main data set: DIUx-xView. (n.d.). DIUx-xView/xView2_baseline. Retrieved from https://github.com/DIUx-xView/xView2_baseline/tree/master/utils
*	Proof of concept data: set:https://ieee-dataport.org/open-access/detecting-damaged-buildings-post-hurricane-satellite-imagery-based-customized#files
*	Text Book:
Hands on Machine Learning with Scikit -Learn, Keras, and Tensorflow by Aurélien Géron.
