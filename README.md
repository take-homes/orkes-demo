## Hello World Application Using Conductor

We will create a simple "Hello World" application that executes a "greetings" workflow managed by Conductor.

### Step 1: The Worker Task

A worker represents a function with the `worker_task` decorator.

In `greetings_worker.py` we have a simple `greet` function. The function simply echos the string provided to it.

<button data-command="open:greetings_worker.py">Open `greetings_worker.py`</button>

Turning this function into a worker task is as simple as adding the following code.  Use the `insert` button to add this chunk of code to `greetings_worker.py`.

<insert-text file="./greetings_worker.py" line="0" col="0">
```python
from conductor.client.worker.worker_task import worker_task
@worker_task(task_definition_name='greet')
```
</insert-text>

Now, we are ready create our first workflow.

### Step 2: The Workflow

We've already defined the workflow in `greetings_workflow.py` take a look:

<button data-command="open:greetings_workflow.py">Open `greetings_workflow.py`</button>


### Step 3: Register Workflow and Launch Task Handler
Now, let's write the code that will register the workflow and provision a `TaskHandler` that will run our task.

<button data-command="open:workflow_runner.py">Open `workflow_runner.py`</button>

Lets build the registration function:

<insert-text file="./workflow_runner.py" line="7" col="0">
```python
def register_workflow(workflow_executor: WorkflowExecutor) -> ConductorWorkflow:
    workflow = greetings_workflow(workflow_executor=workflow_executor)
    workflow.register(True)
    return workflow

```
</insert-text>

<insert-text file="./workflow_runner.py" line="15" col="0">
```python
def main():
    api_config = Configuration()
    workflow_executor = WorkflowExecutor(configuration=api_config)
    workflow = register_workflow(workflow_executor)
    task_handler = TaskHandler(configuration=api_config)
    task_handler.start_processes()
    task_handler.join_processes()

```
</insert-text>

### Step 4: Create the Task Runner
Finally, we can write the code that will run our task.

Insert the following code into `task_runner.py`.

<insert-text file="./task_runner.py" line="4" col="0">
```python
def main():
    api_config = Configuration()
    workflow_executor = WorkflowExecutor(configuration=api_config)
    workflow_req = StartWorkflowRequest(name="greetings", version=1, input={"name": "orkes"})
    workflow_run = workflow_executor.start_workflow(workflow_req)
    print(f'\nworkflow result: {workflow_run}\n')
    print(f'see the workflow execution here: {api_config.ui_host}/execution/{workflow_req}\n')
```
</insert-text>


### Running Workflows on Conductor

Now we are ready to execute our workflow!

First we will register the workflow and provision our TaskHandler. Run the following:

<button data-command="run:python workflow_runner.py">Run `workflow_runner.py`</button>

And now lets run our `greet` task.  Run the following:

<button data-command="run:python task_runner.py">Run `task_runner.py`</button>

Now, the workflow is executed, and its execution status can be viewed in the [Conductor UI](http://localhost:5000).