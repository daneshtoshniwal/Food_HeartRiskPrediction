# Food Health Risk Prediction

This project utilizes the Gemini API and a meta-prompting technique to analyze a food image, identify the food item, and then estimate the associated heart health risk based on its nutritional content.

## Features

* **Food Identification:** Identifies the food item from an uploaded image using the Gemini Vision model.
* **Nutritional Analysis:** Quantifies key nutritional components (Calories, Saturated Fat, Trans Fat, Naturally Occurring Sugar, Refined Sugar, and Salt) for the identified food item (per 100g/ml) using the Gemini Pro model and structured output.
* **Heart Risk Estimation:** Calculates an estimated heart risk percentage based on predefined thresholds for the identified nutritional components.
* **User-friendly Interface:** Provides a simple web interface using Gradio for easy image upload and result display.

## How It Works

The core of this project lies in the intelligent use of the Gemini API through a "meta-prompting" approach:

1.  **Image Input & Initial Prompt (Vision Model):** The user uploads a food image. This image is sent to the `gemini-2.0-flash` (a Vision model) with a concise prompt: "What is this food item called? Just give me the food item name and nothing else." This allows the model to focus solely on identifying the food.

2.  **Food Name to Detailed Analysis (Language Model with Structured Output):** The identified food name from the previous step is then used as an input for the next prompt. A more detailed prompt is crafted and sent to `gemini-2.0-flash` (a Language model): `Analyze the food item: '{food_description}' and give me the amount of every ingredient in list: {"Calories","Saturated Fat","Trans Fat","Naturally occurring Sugar","Refined sugar","Salt"} to create 100 grams of '{food_description}'.` Crucially, this prompt also specifies a `response_mime_type` of `application/json` and a `response_schema` defined by the `IngredientQuantity` Pydantic model. This forces the Gemini model to return the nutritional information in a structured, parseable JSON format, ensuring data consistency and ease of extraction.

3.  **Risk Calculation (Python Logic):** The extracted nutritional data (e.g., quantity of Saturated Fat, Calories, Salt) is then processed by a custom Python function. Predefined `risk_thresholds` are used to determine the individual risk contribution of each nutritional component. These thresholds are set based on general dietary guidelines (e.g., higher trans fat leads to higher risk). A combined heart risk probability is calculated by multiplying the inverse of individual component risks. The total heart risk is then `1 - combined_risk_probability`.

4.  **Gradio Interface:** The entire process is wrapped in a Gradio application, providing a user-friendly web interface to upload images and view the food description, detailed analysis, and the estimated heart risk.

## Installation and Setup

1.  **Clone the Repository:**

    ```bash
    git clone <repository_url>
    cd Food_HeartRiskPrediction
    ```

2.  **Create a Virtual Environment (Recommended):**

    ```bash
    python -m venv env
    source env/bin/activate  # On Windows: env\Scripts\activate
    ```

3.  **Install Dependencies:**

    ```bash
    pip install -r requirements.txt
    ```

    (The `requirements.txt` file should contain: `google-generativeai`, `Pillow`, `pydantic`, `requests`, `gradio`)

4.  **Obtain Gemini API Key:**
    * Go to the [Google AI Studio](https://aistudio.google.com/app/apikey) and generate your API key.
    * Save your API key in a file named `key.txt` in the root directory of the project (e.g., `/content/key.txt` as shown in the notebook).

## Usage

1.  **Run the Jupyter Notebook (Colab or Local):**
    Open the `Food_HeartRiskPrediction.ipynb` notebook in Google Colab or a local Jupyter environment.

2.  **Execute Cells:**
    Run all the cells in the notebook sequentially.

3.  **Access Gradio Interface:**
    Once the last cell (Gradio app) is executed, a public URL will be provided in the output (if running on Colab with `share=True`). Open this URL in your web browser.

4.  **Upload Image:**
    In the Gradio interface, click on the "📷 Upload Food Image" section and select a food image from your local machine.

5.  **Analyze:**
    Click the "🚀 Analyze Image" button to get the food description, detailed nutritional analysis, and the estimated heart risk.

## Code Structure

* `Food_HeartRiskPrediction.ipynb`: The main Jupyter notebook containing all the code.
    * API key loading.
    * Necessary imports.
    * `food_content`: Set of nutritional components to analyze.
    * `IngredientQuantity` (Pydantic Model): Defines the expected JSON structure for nutritional analysis.
    * `what_is_this_food(food_image)`: Function to identify the food item from an image using Gemini Vision.
    * `get_food_analysis(food_description)`: Function to get detailed nutritional analysis using Gemini Pro with structured output.
    * `risk_thresholds`: Dictionary defining risk percentages for different quantities of nutritional components.
    * `get_component_risk(component_name, quantity, thresholds)`: Calculates individual component risk.
    * Main logic to calculate total heart risk.
    * Gradio interface setup and `analyze_food_image` function for the web app.
