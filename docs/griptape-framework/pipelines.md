# Pipelines

Pipelines are lists of tasks that are executed sequentially. Pipelines can have `Memory`, which makes them ideal for storing LLM conversations.

Here is an example of a pipeline:

```python
from griptape import utils
from griptape.memory import Memory
from griptape.tasks import PromptTask
from griptape.structures import Pipeline


pipeline = Pipeline(
    memory=Memory()
)

pipeline.add_tasks(
    # take the first argument from the pipeline `run` method
    PromptTask("{{ args[0] }}"),
    # take the input from the previous task and insert it into the prompt
    PromptTask("Say the following like a pirate: {{ input }}")
)

pipeline.run("I am Scotty, who are you?")
pipeline.run("Who am I?")

print(utils.Conversation(pipeline.memory).to_string())
```

Boom! Our first conversation, à la ChatGPT, is here:

> Q: I am Scotty, who are you?  
> A: Arrr, I be an AI language model designed to assist and answer yer questions, matey!  
> Q: Who am I?  
> A: Yarrr, ye just introduced yerself as Scotty, so ye be Scotty, matey!

## Prompt Context

You can dynamically pass arguments to the prompt by using Jinja templates:

```python
PromptTask("tell me about {{ topic }}", context={"topic": "the lord of the rings"})
```

In addition to user-defined fields, the `context` object contains the following:

In `Pipeline` structures:
- `args`: arguments passed to the `Construct.run()` method.
- `input`: input from the parent.
- `structure`: the structure that the task belongs to.
- `parent`: parent task.
- `child`: child task.

**griptape** uses OpenAI's `gpt-3.5-turbo` model by default. If you want to use a different model, set a custom OpenAI prompt driver:

```python
Pipeline(
    prompt_driver=OpenAiPromptDriver(
        model="gpt-4"
    )
)
```

## Prompt Drivers

You can specify a custom `prompt_driver` for pipelines and workflows (e.g., to specify a different model):

```python
pipeline = Pipeline(
    prompt_driver=OpenAiPromptDriver(
        model="gpt-4"
    )
)
```