# Prompt Engineering for Developers

## Introduction

- Two types of Large Language Models (LLMs):
  - **Base LLM:**
    - Predicts next word based on text training data.
  - **Instruction Tuned LLM:**
    - Tries to follow instructions.
    - Fine-tuned on instructions and good attemps at following instructions.

## Guidelines

- **Principles of Prompting:**

  - *Write clear and specific instructions.*
    1. Use delimeters to clearly indicate distinct parts of the input.
      - Delimeters include:
        - Backticks: ```
        - Quotations: """
        - Brackets: <>
        - Tags: <tag></tag>
      - Prevents prompt injections and conflicting instructions.
      - Eg.
      ```
      Python
      text = f"""
      You should express what you want a model to do by \ 
      providing instructions that are as clear and \ 
      specific as you can possibly make them. \ 
      This will guide the model towards the desired output, \ 
      and reduce the chances of receiving irrelevant \ 
      or incorrect responses. Don't confuse writing a \ 
      clear prompt with writing a short prompt. \ 
      In many cases, longer prompts provide more clarity \ 
      and context for the model, which can lead to \ 
      more detailed and relevant outputs.
      """
      prompt = f"""
      Summarize the text delimited by triple quotes into a single sentence.
      \"\"\"{text}\"\"\"
      """

      response = get_completion(prompt)
      print(response)
      ```
    2. Ask for a structured output like HTML or JSON.
      - E.g.
      ```
      prompt = f"""
      Generate a list of three made-up book titles along \ 
      with their authors and genres. 
      Provide them in JSON format with the following keys: 
      book_id, title, author, genre.
      """
      response = get_completion(prompt)
      print(response)
      ```
    3. Ask and Check if conditions are satisfied.
      - E.g.
      ```
      text_1 = f"""
      Making a cup of tea is easy! First, you need to get some \ 
      water boiling. While that's happening, \ 
      grab a cup and put a tea bag in it. Once the water is \ 
      hot enough, just pour it over the tea bag. \ 
      Let it sit for a bit so the tea can steep. After a \ 
      few minutes, take out the tea bag. If you \ 
      like, you can add some sugar or milk to taste. \ 
      And that's it! You've got yourself a delicious \ 
      cup of tea to enjoy.
      """
      prompt = f"""
      You will be provided with text delimited by triple quotes. 
      If it contains a sequence of instructions, \ 
      re-write those instructions in the following format:

      Step 1 - ...
      Step 2 - …
      …
      Step N - …

      If the text does not contain a sequence of instructions, \ 
      then simply write \"No steps provided.\"

      \"\"\"{text_1}\"\"\"
      """
      response = get_completion(prompt)
      print("Completion for Text 1:")
      print(response)
      ```
    4. Few-shot prompting.
      - Give successful examples of completing task.
      - E.g.
      ```
      prompt = f"""
      Your task is to answer in a consistent style.

      <child>: Teach me about patience.

      <grandparent>: The river that carves the deepest \ 
      valley flows from a modest spring; the \ 
      grandest symphony originates from a single note; \ 
      the most intricate tapestry begins with a solitary thread.

      <child>: Teach me about resilience.
      """
      response = get_completion(prompt)
      print(response)
      ```
    
  - *Give the model time to think.*
    1. Specify the steps to complete a task.
      - E.g.
      ```
      prompt = f"""
      Perform the following actions: 
      1 - Summarize the following text delimited by triple \
      quotes with 1 sentence.
      2 - Translate the summary into French.
      3 - List each name in the French summary.
      4 - Output a json object that contains the following \
      keys: french_summary, num_names.

      Separate your answers with line breaks.

      Text:
      \"\"\"{text_1}\"\"\"
      """
      ```
    2. Instruct the model to work out its own solution before rushing to a conclusion.
      - E.g.
      ```
      prompt = f"""
      Your task is to determine if the student's solution \
      is correct or not.
      To solve the problem do the following:
      - First, work out your own solution to the problem including the final total. 
      - Then compare your solution to the student's solution \ 
      and evaluate if the student's solution is correct or not. 
      Don't decide if the student's solution is correct until 
      you have done the problem yourself.

      Use the following format:
      Question:
      \`\`\`
      question here
      \`\`\`
      Student's solution:
      \`\`\`
      student's solution here
      \`\`\`
      Actual solution:
      \`\`\`
      steps to work out the solution and your solution here
      \`\`\`
      Is the student's solution the same as actual solution \
      just calculated:
      \`\`\`
      yes or no
      \`\`\`
      Student grade:
      \`\`\`
      correct or incorrect
      \`\`\`

      Question:
      \`\`\`
      I'm building a solar power installation and I need help \
      working out the financials. 
      - Land costs $100 / square foot
      - I can buy solar panels for $250 / square foot
      - I negotiated a contract for maintenance that will cost \
      me a flat $100k per year, and an additional $10 / square \
      foot
      What is the total cost for the first year of operations \
      as a function of the number of square feet.
      \`\`\` 
      Student's solution:
      \`\`\`
      Let x be the size of the installation in square feet.
      Costs:
      1. Land cost: 100x
      2. Solar panel cost: 250x
      3. Maintenance cost: 100,000 + 100x
      Total cost: 100x + 250x + 100,000 + 100x = 450x + 100,000
      \`\`\`
      Actual solution:
      """
      response = get_completion(prompt)
      print(response)
      ```
    
  - **Model Limitations:**

    - *Hallucination:* Makes statements that sound plausible but are not true.
      - Reducing Hallucinations:
        - Ask the model to first find relevent information then answer the question based on the relevent information.

## Iterative

- Iterative Prompt Development (Repeasted until satisfied with the result):
  - Idea
  - Implementation
  - Experimental Result
  - Error Analysis
- Start with the idea and test it, if the result is not up to the standard, refine the idea - add more details and instructions - until the ideal result is reached.
- E.g.
  - *Iteration 1:*
  ```
  prompt = f"""
  Your task is to help the marketing team create a descritpion for a product using a technical fact sheet.

  Write a product description based on the information provided in the technical specifications delimited by triple backticks.

  \`\`\` {text} \`\`\`
  """
  ```
  - *Iteration 2:*
  ```
  prompt = f"""
  Your task is to help the marketing team create a descritpion for a product using a technical fact sheet.

  Write a product description based on the information provided in the technical specifications delimited by triple backticks.

  Use at most 50 words.

  \`\`\` {text} \`\`\`
  """
  ```
  - *Iteration 3:*
  ```
  prompt = f"""
  Your task is to help the marketing team create a descritpion for \
  a product using a technical fact sheet.

  Write a product description based on the information provided in the technical specifications delimited by triple backticks.

  Use at most 50 words.
  Include the product number in the description.

  \`\`\` {text} \`\`\`
  """
  ```

## Summarizing

- Using the iterative process, create prompt to summarize a text.
- The edit to summerize as per specific aspects, such as price, quality, sentiment, etc. from the given text.
- E.g.
  - *Iteration 1:*
  ```
  prompt = f"""
  Your task is to generate a short summary of a product \
  review from an ecommerce site. 

  Summarize the review below, delimited by triple 
  backticks, in at most 30 words. 

  Review: \`\`\`{prod_review}\`\`\`
  """
  ```
  - *Iteration 2:* Focus on shipping information.
  ```
  prompt = f"""
  Your task is to generate a short summary of a product \
  review from an ecommerce site to give feedback to the \
  Shipping deparmtment. 

  Summarize the review below, delimited by triple 
  backticks, in at most 30 words, and focusing on any aspects \
  that mention shipping and delivery of the product. 

  Review: \`\`\`{prod_review}\`\`\`
  """
  ```
- This method can also be used to sxtract specific information.
- E.g.
  ```
  prompt = f"""
  Your task is to extract relevant information from \ 
  a product review from an ecommerce site to give \
  feedback to the Shipping department. 

  From the review below, delimited by triple quotes \
  extract the information relevant to shipping and \ 
  delivery. Limit to 30 words. 

  Review: \`\`\`{prod_review}\`\`\`
  """
  ```

## Inferring

- Use for inferring sentiments and topics from information.

- The different examples of tasks that can be performed:
  
  - **Positive/Negative Sentiment:**
    - E.g.
    ```
    prompt = f"""
    What is the sentiment of the following product review, 
    which is delimited with triple backticks?

    Review text: '''{lamp_review}'''
    """
    ```
    OR
    ```
    prompt = f"""
    What is the sentiment of the following product review, 
    which is delimited with triple backticks?

    Give your answer as a single word, either "positive" \
    or "negative".

    Review text: '''{lamp_review}'''
    """
    ```
  
  - **Type of Emotion:**
    - E.g.
    ```
    prompt = f"""
    Identify a list of emotions that the writer of the \
    following review is expressing. Include no more than \
    five items in the list. Format your answer as a list of \
    lower-case words separated by commas.

    Review text: '''{lamp_review}'''
    """
    ```
  
  - **Specific Emotion (Anger):**
    - E.g.
    ```
    prompt = f"""
    Is the writer of the following review expressing anger?\
    The review is delimited with triple backticks. \
    Give your answer as either yes or no.

    Review text: '''{lamp_review}'''
    """
    ```

  - **Extract Information:**
    - E.g.
    ```
    prompt = f"""
    Identify the following items from the review text: 
    - Item purchased by reviewer
    - Company that made the item

    The review is delimited with triple backticks. \
    Format your response as a JSON object with \
    "Item" and "Brand" as the keys. 
    If the information isn't present, use "unknown" \
    as the value.
    Make your response as short as possible.
      
    Review text: '''{lamp_review}'''
    """
    ```
  
  - **Perform Multiple Tasks:**
    - E.g.
    ```
    prompt = f"""
    Identify the following items from the review text: 
    - Sentiment (positive or negative)
    - Is the reviewer expressing anger? (true or false)
    - Item purchased by reviewer
    - Company that made the item

    The review is delimited with triple backticks. \
    Format your response as a JSON object with \
    "Sentiment", "Anger", "Item" and "Brand" as the keys.
    If the information isn't present, use "unknown" \
    as the value.
    Make your response as short as possible.
    Format the Anger value as a boolean.

    Review text: '''{lamp_review}'''
    """
    ```
  
  - **Inferring Topics:**
    - E.g.
    ```
    prompt = f"""
    Determine five topics that are being discussed in the \
    following text, which is delimited by triple backticks.

    Make each item one or two words long. 

    Format your response as a list of items separated by commas.

    Text sample: '''{story}'''
    """
    ```
  
## Transforming

- LLMs csn be used for text transformation tasks such as:

  - **Translation:**
    - *Translating the text:*
      ```
      prompt = f"""
      Translate the following English text to Spanish: \ 
      \`\`\`Hi, I would like to order a blender\`\`\`
      """
      ```
    - *Identifing the Language:*
      ```
      prompt = f"""
      Tell me which language this is: 
      \`\`\`Combien coûte le lampadaire?\`\`\`
      """
      ```
    - *Translating with Form:*
      ```
      prompt = f"""
      Translate the following text to Spanish in both the \
      formal and informal forms: 
      'Would you like to order a pillow?'
      """
      ```
  
  - **Tone Transformation:**
    - E.g.
    ```
    prompt = f"""
    Translate the following from slang to a business letter: 
    'Dude, This is Joe, check out this spec on this standing lamp.'
    """
    ```
  
  - **Format Conversion:**
    - E.g.
    ```
    data_json = { "resturant employees" :[ 
        {"name":"Shyam", "email":"shyamjaiswal@gmail.com"},
        {"name":"Bob", "email":"bob32@gmail.com"},
        {"name":"Jai", "email":"jai87@gmail.com"}
    ]}

    prompt = f"""
    Translate the following python dictionary from JSON to an HTML \
    table with column headers and title: {data_json}
    """
    ```

  - **Spelling or Grammer Check:**
    - E.g.
    ```
    prompt = f"""Proofread and correct the following text
    and rewrite the corrected version. If you don't find
    and errors, just say "No errors found". Don't use 
    any punctuation around the text:
    \`\`\`{t}\`\`\`"""
    ```