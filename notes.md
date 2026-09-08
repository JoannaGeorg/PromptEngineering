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
