# Understanding mkdir

### Question: what does this do: mkdir -p html node-app flask-app
`mkdir` creates directories.

`-p` means:

* create parent directories as needed
* do not throw an error if the directories already exist

This command:

```bash
mkdir -p html node-app flask-app
```

creates three directories in the current working directory:

```text
html/
node-app/
flask-app/
```

Result:

* `html` could hold static web files
* `node-app` could hold a Node.js application
* `flask-app` could hold a Python Flask application

Example before:

```text
project/
```

After running:

```text
project/
├── html/
├── node-app/
└── flask-app/
```


### If I were to run the same command with a -p, what would it do? 


You already used `-p` in the original command.

```bash id="78hnad"
mkdir -p html node-app flask-app
```

Without `-p`:

```bash id="o5sqzs"
mkdir html node-app flask-app
```

Difference:

* If the directories already exist:

  * `mkdir` → throws an error
  * `mkdir -p` → silently succeeds

* If parent directories are missing:

  * `mkdir` → fails
  * `mkdir -p` → creates the entire path automatically

Example:

```bash id="s6tt4x"
mkdir project/frontend/html
```

Fails if `project/` or `frontend/` do not exist.

But:

```bash id="4lvw7g"
mkdir -p project/frontend/html
```

creates all missing directories in one step.

