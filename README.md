<div id="top"></div>

<h1>Udacity Generative AI</h1>

<!-- TABLE OF CONTENTS -->
<summary>Table of Contents</summary>
<ol>
    <li>
        <a href="#about-the-project">About The Project</a>
        <ul>
            <li><a href="#folder-structure">Folder Structure</a></li>
            <li><a href="#datasets">Datasets</a></li>
        </ul>
    </li>
    <li>
        <a href="#implementation">Implementation</a>
        <ul>
            <li><a href="#design-considerations">Design Considerations</a></li>
            <li><a href="#getting-started">Getting Started</a></li>
        </ul>
    </li>
    <li><a href="#troubleshooting">Troubleshooting</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
</ol>

<!-- ABOUT THE PROJECT -->
## About The Project

This project is for the Udacity's Generative AI Nanodegree program. 

The following are the requirements for the project:
1. Understanding Buyer Preferences
    - Buyers will input their requirements and preferences, such as location, property type, budget, amenities, and lifestyle choices.
    - The application uses LLMs to interpret these inputs in natural language, understanding nuanced requests beyond basic filters.

2. Integrating with a Vector Database
    - Connect "HomeMatch" with a vector database, where all available property listings are stored.
    - Utilize vector embeddings to match properties with buyer preferences, focusing on aspects like neighborhood vibes, architectural styles, and proximity to specific amenities.

3. Personalized Listing Description Generation
    - For each matched listing, use an LLM to rewrite the description in a way that highlights aspects most relevant to the buyer’s preferences.
    - Ensure personalization emphasizes characteristics appealing to the buyer without altering factual information about the property.

4. Listing Presentation
    - Output the personalized listing(s) as a text description of the listing


<p align="right">(<a href="#top">back to top</a>)</p>

<!-- Folder Structure -->
### Folder Structure

The following are the files found in the workspace

```
.
├── home_matching
├── HomeMatch.ipynb
├── images
├── listings.csv
├── README.md
```

|#|File/Folder|Description|
|---|---|---|
|1|home_matching|Virtual Environment for executing the jupyter nobook|
|2|HomeMatch.ipynb|Application that contains the logic to save to the chromadb and allow search for the chromadb details|
|3|images|Screenshots for the README.md|
|4|listings.csv|Data file containing the property details|
|5|README.md|Contain information regarding the application and the setup|

<p align="right">(<a href="#top">back to top</a>)</p>

<!-- datasets -->
### Datasets

The generated property listings is stored in `listings.csv`. <br>
The listings are generated with the following prompt:
```
You are a property guru in singapore. I need 15 listing of house around singapore. Can you generate them with the following fields: Neighborhood, Price, Bedrooms, Bathroom, House Size, Description, Neigborhood Description
```

The dataset contains the following columns:
|#|Field|Description|Example
|:---|:---|:---|:---|
|1|Neighborhood|The location where the property resides|Orchard Road|
|2|Price (SGD)|The cost of purchasing the property|3500000|
|3|Bedrooms|The number of bedrooms the property has|3|
|4|Bathrooms|The number of bathrroms the property has|2|
|5|House Size (sq ft)|The dimension of the property|1200|
|6|Description|A brief description about the property|Luxurious apartment with modern amenities and stunning city views.|
|7|Neighborhood Description|A brief description about the neighborhood|Orchard Road is a vibrant shopping district known for its high-end retail and dining|

<p align="right">(<a href="#top">back to top</a>)</p>

<!-- IMPLMENTATION -->
## Implementation

<p align="right">(<a href="#top">back to top</a>)</p>

<!-- Design Considerations -->
### Design Considerations

The following depicts the workflow for the application

![Application Workflow][inteface-workflow]

<br>

The application can be broken down into 2 areas
- Storing Property Listing to Vector Database
- Search Vector Database for User Recommendation

#### Storing Property Listing to Vector Database

The following is the workflow:
- User will trigger **manually** to a LLM model (E.g `ChatGPT`) to generate the sample property listing
- User will save the property listing into the `Chromadb` vector database
- Since there are multiple fields for the sample property listing, all the columns will be **concatenated** and their generated embeddings will be stored into `Chromadb`
- We will also be using `BertTokenizer` from `huggingface` for the **embedding function** as previous trial using `Chromadb`-provided **embedding function** leads to embeddings of various size and `Chromadb` is complaining when trying to add to the collection.

#### Search Vector Database for User Recommendation

The following is the workflow:
- Once we have the embeddings in the `Chromadb`, we can prompt User for a few filter questions so that the application can generate a **personalized** information for the User
- Similarly to the property listing, since there are multiple questions, we will **concatenate** the answers together and generate the embeddings of the answer to be search with the `Chromadb`
- `Chromadb` will return the result according to the distances (i.e The smaller the distance the more similarity)
- The answer will be passed to a `LLM` model (E.g `gpt-3.5-turbo`) to generate the **rationale** and **personalized** information for the User
- Since we don't want the application to provide incorrect data, in our **prompt template**, we will add in a **guardrail** to ask the model to just focus on the answer and user preferences provided

<p align="right">(<a href="#top">back to top</a>)</p>

<!-- GETTING STARTED -->
### Getting Started

The following are the steps to run the application:
1. First we have to run `HomeMatch.ipynb` section `Import Libraries` till `Personalizing Listing Descriptions` to load the necessary libraries and configurations.
2. Once done, we can then run the codes under the section `Building the User Preference Interface`
3. You will be prompted with the following questions and the personalized recommendations will be generated once all the answers are provided

<br>

|#|question|example|
|:---|:---|:---|
|1|How big do you want your house to be?|A comfortable three-bedroom house with a spacious kitchen and a cozy living room.|
|2|What are 3 most important things for you in choosing this property?|A quiet neighborhood, good local schools, and convenient shopping options.|
|3|Which amenities would you like?|A backyard for gardening, a two-car garage, and a modern, energy-efficient heating system.|
|4|Which transportation options are important to you?|Easy access to a reliable bus line, proximity to a major highway, and bike-friendly roads.|
|5|How urban do you want your neighborhood to be?|A balance between suburban tranquility and access to urban amenities like restaurants and theaters.|
<br/>

The following show a snapshot of the questions and answers:

![User Input][interface-input]

The following show the result return by the llm:

![User Recommendation][interface-personalized-recommendations]

<p align="right">(<a href="#top">back to top</a>)</p>

<!-- troubleshooting -->
### Troubleshooting

#### Issue #1: Unable to install new library in Jupyter Notebook

Example:
```txt
ModuleNotFoundError                       Traceback (most recent call last)
Cell In[3], line 2
      1 import os
----> 2 import pandas as pd
      3 from langchain.llms import OpenAI
      4 import chromadb.utils.embedding_functions as embedding_functions

ModuleNotFoundError: No module named 'pandas'
```

Possible Solution:
- Check if the notebook is `Trusted`, if not set to `Trusted`
- After running the `pip install` command, restart the kernel and try to import the module again

![Module Installed Problem][troubleshooting-trust-notebook]
<br/>


<p align="right">(<a href="#top">back to top</a>)</p>

<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

Here are some resources that have helped in completing this project!

* [Readme Template][readme-template-url]
* [Chroma Documentation][chroma-documentation-url]
* [OpenAI Documentation][openai-documentation-url]
* [HuggingFace BertTokenizer Documentation][huggingface-bert-documentation-url]

<p align="right">(<a href="#top">back to top</a>)</p>

<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->

[inteface-workflow]: ./images/Interface_Workflow.png
[interface-input]: ./images/Interface_Input.png
[interface-personalized-recommendations]: ./images/Interface_Personalized_Recommendation.png
[troubleshooting-trust-notebook]: ./images/Troubleshooting_Setting_Notebook_To_Trusted.png
[readme-template-url]: https://github.com/othneildrew/Best-README-Template/blob/master/README.md
[chroma-documentation-url]: https://docs.trychroma.com/docs/overview
[openai-documentation-url]: https://platform.openai.com/docs/overview
[huggingface-bert-documentation-url]: https://huggingface.co/google-bert/bert-base-uncased