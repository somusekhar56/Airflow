# Airflow_documentation
# Apache Airflow – Core Concepts Documentation
# Overview
Apache Airflow is an open-source workflow orchestration platform used to schedule, monitor, and manage workflows.

Workflows are defined as code (Python), which makes them dynamic, version-controlled, and scalable.

Airflow does not process data itself.

Instead, it coordinates and orchestrates tasks that run in external systems such as databases, APIs, cloud services, or compute engines.

# 1. Introduction to Apache Airflow
Apache Airflow helps in managing complex workflows by:

Defining task dependencies

Scheduling task execution

Handling retries and failures

Providing monitoring and logging
# Airflow is widely used in:

Data engineering pipelines

ETL / ELT workflows

Batch processing systems

Machine learning orchestration
# 1.1 Core Concepts
Airflow is built on four core concepts:

DAGs


Operators

Tasks

# DAG-to-DAG triggers
Understanding these concepts is essential before building pipelines.

# 1.1.1 DAG Definition (Directed Acyclic Graph)
# What is a DAG?
A DAG (Directed Acyclic Graph) represents a workflow definition in Airflow.

Directed: Tasks have a defined execution order

Acyclic: No circular dependencies are allowed

Graph: Tasks are nodes, dependencies are edges

A DAG defines what should run and in what order, but it does not execute tasks itself.

# Key Characteristics
Written in Python

Parsed continuously by the scheduler

Represents workflow structure only

Execution happens through DAG Runs

Conceptual Understanding
# A DAG is like a blueprint:

It describes the plan

It does not perform the work

Tasks are executed based on this plan
# 1.1.2 Operators
What is an Operator?
An Operator defines what kind of action should be performed.

Operators act as templates for tasks.

# Examples:

Run a Python function

Execute a shell command

Send an email

Trigger another DAG

# Common Operator Types
Action Operators: Perform actions (PythonOperator, BashOperator)

Sensor Operators: Wait for conditions (FileSensor, ExternalTaskSensor)

Transfer Operators: Move data between systems

Control Flow Operators: Control execution path
3 1.1.3 Tasks
# What is a Task?
A Task is a single execution unit in a DAG.

It is created when an operator is instantiated inside a DAG.

Task Lifecycle

Task is created by the scheduler

Task waits for dependencies

Task is queued

Executor runs the task

Status is updated (success / failed / retry)

Task Characteristics

Atomic (does one job)

Independent

Retryable

Stateless (recommended)
# 1.1.4 DAG to DAG Trigger
What is DAG-to-DAG Triggering?

DAG-to-DAG triggering allows one DAG to start another DAG.

# This is useful when workflows need to be:

Modular

Reusable

Decoupled
# How It Works
A task in DAG A triggers DAG B

DAG B starts a new DAG Run

DAGs operate independently after triggering
# Use Cases
Breaking large workflows into smaller DAGs

Event-based orchestration

Multi-stage pipelines (ingestion → transformation → reporting)
# 1.2 Scheduler
What is the Scheduler?

The Scheduler is the component responsible for deciding when tasks should run.

# It continuously:

Reads DAG files

Creates DAG Runs

Evaluates task dependencies

Sends runnable tasks to the executor

# Responsibilities
DAG parsing

Scheduling DAG Runs

Dependency resolution

Task queuing

Important Note
# The scheduler:

Does NOT execute tasks

Does NOT run business logic

Only makes scheduling decisions
# 1.3 Executor
What is an Executor?

The Executor determines how and where tasks are executed.

It acts as a bridge between:

Scheduler

Workers

Execution Flow

# 6. Workflow Management
What it is

Designing, scheduling, monitoring, and retrying tasks in a controlled way.

# Why it matters
Ensures reliable data pipelines

Handles failures and retries automatically
# Example
A DAG with retries, dependencies, and scheduling is workflow management.

# 7. Default Arguments
# What it is
Common parameters shared across tasks.

# Why we use it
Avoid repetition

Centralized control

# Example
default_args = {

    'owner': 'airflow',
    
    'retries': 2,
    
    'retry_delay': timedelta(minutes=5)
}
# 8. Schedule Interval
# What it is
Defines when and how often a DAG runs.

# Examples
schedule_interval='@daily'

schedule_interval='0 2 * * *'

schedule_interval=None  # Manual only
# 9. Catchup
# What it is
Whether Airflow should run past missed DAG runs.

# Why important
Avoid unexpected backfills.

# Example
catchup=False
# 10. Task Dependencies
# What it is
Defines execution order of tasks.

# Example
task1 >> task2
# 11. set_upstream() / set_downstream()
# What it is
Programmatic way to set dependencies.

# Example
task2.set_upstream(task1)

task1.set_downstream(task2)
# 🔹 REAL-WORLD SCENARIOS
# 1. File-based DAG trigger (wait for file arrival)
# Use case
Run pipeline only when a file arrives.

# Example
FileSensor(...)
# 2. DAG-to-DAG dependency handling
Use case

One pipeline depends on another.

# Example
TriggerDagRunOperator(...)
# 3. Conditional task execution (branching)
# Use case
Run different tasks based on conditions.

# Example
from airflow.operators.branch import BranchPythonOperator
# 4. Sequential vs Parallel task execution
Sequential

t1 >> t2 >> t3

Parallel

t1 >> [t2, t3]

# 5. Task retry on transient failure
Use case

Temporary API/network failures.

# Example
retries=3
retry_delay=timedelta(minutes=2)
# 6. Partial DAG failure handling
# Use case
Some tasks fail, others succeed.

# How Airflow handles
Only failed tasks marked failed; downstream blocked.

# 7. Rerunning only failed tasks
How

UI → Clear failed tasks

Scheduler reruns only failed ones
# 8. Backfilling historical data
# Use case
Load past data.

# Command
airflow dags backfill dag_id -s 2024-01-01 -e 2024-01-05
# 9. Preventing duplicate data on reruns
Techniques

Idempotent logic

Merge instead of insert

Use execution_date
# 10. Manual trigger vs scheduled run mismatch
Problem

Manual runs don’t match scheduled dates.

# Solution
Use {{ ds }} instead of system date.

# 11. Handling long-running sensors
Problem

Sensors occupy worker slots.

# Solution
Use reschedule mode.

# Example
mode='reschedule'



