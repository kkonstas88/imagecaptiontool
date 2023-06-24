</br>

# <u>**Automate Fashion Image Captioning using BLIP-2**</u> # 
</br>
The fashion industry isworth trillions of dollars. The goal of any company/seller is to help customer tofind the right product from a huge corpus of products that they are searchingfor. So, when customer find the right product they are mostly going to addthe item to their cart and which help in company revenue.</br>
Accurate and enchanting descriptions of clothes on shopping websites can help customers without fashion knowledge to better understand the features (attributes, style, functionality, etc.) of the items and increase online sales by enticing more customers. Also, most of the time when any customer visits shopping websites, they are looking for a certain style or type of clothes that wish to purchase, they search for the item by providing a description of the item and the system finds the relevant items that match the search query by computing the similarity score between the query and the item caption. In such use cases having an accurate description of the clothes is useful.</br>
Manually writing the descriptions is a non-trivial and highly expensive task. Thus, the automatic generation of descriptions is an urgent need and will help the seller (while uploading the product to recommend captions).
</br>
</br>
</br>

# **Problem Statement** #
</br>
Given the clothes imageprovide a short caption that describes the item. In general, in image captioningdatasets (e.g., COCO, Fliker), the descriptions of fashion items have threeunique features, which makes the automatic generation of captions a challengingtask. First, fashion captioning needs to describe the attributes of an item,while image captioning generally narrates the objects and their relations inthe image.</br>
e.g. image where the model is wearing a shirt, the generalcaption model describes such images as "male wearing a white shirt".This is incorrect since we want the model to describe the item. In thisapplication, it is much more important to have a performant to caption theimage than an interpretable model.
</br>
</br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <img src="images\challange_image_text.png"/>
</br>
</br>

# <u>**Dataset**</u> #
**FAshion CAptioning Dataset (FACAD)**, the fashion captioning dataset consisting of over 993K images. 

Properties of FACAD dataset:
1. Diverse fashion images of all four seasons, ages (kids and adults), categories (clothing, shoes, bag, accessories,
etc.), angles of a human body (front, back, side, etc.). 
2. It tackles the captioning problem for fashion items.
    * FACAD contains fine-grained descriptions of attributes of fashion-related items, while MS COCO narrates the objects and their relations in general images. FACAD has longer captions (21 words per sentence on average) compared with the 10.4 words per sentence of the MS COCO caption dataset
    * Expression style of FACAD is enchanting, while that of MS COCO is plain without rich expressions. e.g. words like "pearly", "so-simple yet so-chic", and 
"retro flair" are more attractive than the plain MS COCO descriptions, like "person in a dress".

</br>
<b><a href="https://github.com/xuewyang/Fashion_Captioning"> Source of the dataset</a></b>
</br>
<b><i>Citation:</i></b>
@inproceedings{
    XuewenECCV20Fashion,
    Author = {Xuewen Yang and Heming Zhang and Di Jin and Yingru Liu and Chi-Hao Wu and Jianchao Tan and Dongliang Xie and Jue Wang and Xin Wang},
    Title = {Fashion Captioning: Towards Generating Accurate Descriptions with Semantic Rewards},
    booktitle = {ECCV},
    Year = {2020}
    }
</br>
</br>
For this project, have only considered 20k images for the pre-trained model due to resource limitations. When a train on the entire dataset would allow the model to see different patterns of thefashion item and the description of the item (which help in the create a largevocabulary that will help in describing new item caption).
</br>
For caption text, each item has to consider itemdescription, color, and brand. When a user search for an item they usuallymention specific color or particular brand along with the style they areinterested to buy. For cleaning caption did not apply to the stem as we want thecaption with proper grammar words. If this was a classification problem weapply stemming, since in that case, predict output is either 1 or 0 whereas inthis case, we want a proper word/sentence.
</br>
</br>
</br>

# **Solution: Using Bootstrapping Language-Image Pre-training with Frozen Image Encoders and Large Language Models (BLIP-2)** #
</br>
BLIP2 is a recent powerful model by Salesforce that is capable of performing visual question answering as well as image captioning.
</br>
</br>

## <u>*Overview on BLIP-2*</u> ##

</br>
<b><a href="https://arxiv.org/abs/2301.12597">BLIP2</a></b> introduces a lightweight module called Querying Transformer (Q-Former) that effectively enhances the vision-language model. Q-Former is a lightweight transformer that uses learnable query vectors to extract visual features from the frozen image encoder. </br>
It acts as an information bottleneck between the frozen image encoder and the frozen Large Language Model (LLM), where it feeds the most useful visual feature for the LLM to output the desired text. 
</br>
BLIP2 has mainly two different versions based on the pre-trained LLM model used:
</br>    1) Open Pre-trained Transformer Language Models(opt-2.7b) by Facebook. Pre-trained model weights in HuggingFace: <b><a href="https://huggingface.co/Salesforce/blip2-opt-6.7b">Salesforce/blip2-opt-6.7b</a></b>
</br>    2) FlanT5 model by Google. Pre-trained model weights in HuggingFace: <b><a href="https://huggingface.co/https://huggingface.co/Salesforce/blip2-flan-t5-xl"> Salesforce/blip2-flan-t5-xl</a></b> or <b><a href="https://huggingface.co/https://huggingface.co/Salesforce/blip2-flan-t5-xxl">Salesforce/blip2-flan-t5-xxl</a></b>
</br>
In both these versions <i>Vision Encoder</i> for image extraction used was Vision Transformer (large-sized model) by Google.
</br>
</br>

## <u>*Architecture of BLIP-2 model*</u> ##
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <img src="images\BLIP-2.png"/>
</br>
</br>

## <u>*Solution*</u> ##

Fine-tune pre-trained model **BLIP2**(trained on Fliker dataset) with Fashion dataset using **Low Rank Adaptation (LoRA)** a **Parameter-efficient fine-tuning technique (PEFT)**
</br>

The original model <b><u><i><a href="https://huggingface.co/Salesforce/blip2-opt-2.7b">Salesforce/blip2-opt-2.7b</a></i></u></b> size was too large. It was quite challenging to fit and fine-tune the model on the 16GB GPU.
</br>
So, for this project have downloaded the pre-trained model <b><u><i><a href="https://huggingface.co/ybelkada/blip2-opt-2.7b-fp16-sharded">ybelkada/blip2-opt-2.7b-fp16-sharded</a></i></u></b> from HuggingFace. This model is trained using OPT-2.7b LLM model with  reduced precision to float16.
</br>
</br>

# **Requirements** #
Refer requirements.txt 
</br>

# **Tech Stack Used** #

1) Python 3.8 </br>
2) HuggingFace `transformers` 🤗</br>
3) peft</br>
4) bitsandbytes</br>

</br>

# **Metric** #
</br>

</br>

# **Results** #
</br>
Train
Valid
Test
</br>
</br>

# **Try it Out** #

Deployed the model on HuggingFace Space. You can try it out from this <b><u><i><a href= "">link</a></i></u></b> 

