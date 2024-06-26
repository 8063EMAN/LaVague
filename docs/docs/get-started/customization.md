# Customizing your Agent

When using our Web Agents, you can customize the LLM, multi-modal LLM (`mm_llm`), embedding model (`embedding`), retriever (`retriever`), prompt template (`prompt_template`) and cleaning function (`extractor`) used as part of the Agent's AI pipeline.

These are attributes of a `Context` object, which can be optionally passed to the `Action Engine` and `World Model`, which in turn are passed to the `Web Agent`.

!!! info "Modifiable elements"

    - **llm**: The `LLM` used by the `Action Engine` to translate text instructions into automation code. You can set the `llm` to any `LlamaIndex LLM object`.

    - **mm_llm**: The `multi-modal LLM` used by the `World Model` to generate the next instruction to be enacted by the Action Engine based on the current state of the web page. You can set the `mm_llm` argument to any `LlamaIndex multi-modal LLM object`.

    - **embedding**: The `embedding model` is used by the `retriever` to convert segments of the HTML page of the target website into vectors, capturing semantic meaning. You can set this to any `LlamaIndex Embedding object`. 

    - **retriever**: The `retriever` is used within the `Action Model` to retrieve the most relevant HTML source code of the webpage to be able to generate the automation code targetting HTML elements. This can be any `LlamaIndex.retriever`.

    - **prompt_template**: The prompt template used by the `Action Engine` to query the `LLM` in order to generate automation code for an instruction. You can replace this with your own prompt template as a string.

    - **extractor**: The `cleaning function` applied to the automation code generated by the LLM in the `Action Engine`. You can replace this with your own custom method as a callable.

These elements are initialized in a `Context` object, which can optionally passed to both the `Action Engine` and `World Model` used by an Agent. If you don't pass them your own Context object, the default OpenaiContext will be used.

!!! abstract "Default Configuration"

    The default configuration is as follows:

    - `llm`: OpenAI's gpt-3.5-turbo,
    - `mm_llm`: OpenAi's gpt-4o,
    - `embedding`: text-embedding-3-large,
    - `retriever`: lavague.core's OpsmSplitRetriever(),
    - `prompt_template`: lavague.core's DefaultPromptTemplate(),
    - `extractor`: lavague.core's PythonFromMarkdownExtractor() function

## Customization an existing Context

Let's take a look at how we can modify specific elements of an existing built-in Context.

#### Example: Modifying a built in context

```python

from llama_index.embeddings.gemini import GeminiEmbedding
from llama_index.llms.gemini import Gemini
from lavague.contexts.openai import OpenaiContext
from llama_index.multi_modal_llms.openai import OpenAIMultiModal
from lavague.core import WebAgent, WorldModel, ActionEngine
from lavague.drivers.selenium import SeleniumDriver

# Initialize the default context
context = OpenaiContext()

# Customize the LLM, multi-modal LLM and embedding models
context.llm = Gemini(model_name="models/gemini-1.5-flash-latest")
context.embedding = GeminiEmbedding(model_name="models/text-embedding-004")
context.mm_llm =  OpenAIMultiModal(model="gpt-4o", temperature=0.0)

# Initialize the Selenium driver
selenium_driver = SeleniumDriver()

# Initialize a WorldModel passing it the custom context
world_model = WorldModel.from_hub("hf_example", context)

# Create an ActionEngine with the customized context
action_engine = ActionEngine(selenium_driver, context)

# Create your agent
agent = WebAgent(action_engine, world_model)
```

Here, we modify the default `OpenaiContext` by replacing its LLM, multi-modal LLM & embedding models. We can then pass this to our `Action Engine`.

## Creating a Context object from scratch

Alternative, you can create a `Context` from scratch by initializing a `lavague.core.Context` object and providing all the Context arguments: 

- `llm`
- `mm_llm`
- `embedding`
- `retriever`
- `prompt_template`
- `extractor`

## Summary

By leveraging the Context module, you can customize the components used by the `Action Engine` and `World Model`, the two key elements leveraged by a `Web Agent`.

You can do this by updating specific elements in one of our built-in contexts like `OpenaiContext`, or by create a new Context from scratch.

You can then pass the customized context to the `Action Engine` and `World Model` during their initialization.