## On MacOs

```jsx
brew tap mongodb/brew
brew update
brew update
```

To run MongoDB (i.e. the [`mongod`](https://www.mongodb.com/docs/manual/reference/program/mongod/#mongodb-binary-bin.mongod) process) **as a macOS service**, run:

```jsx
brew services start mongodb-community@6.0
```

To stop a [`mongod`](https://www.mongodb.com/docs/manual/reference/program/mongod/#mongodb-binary-bin.mongod) running as a macOS service, use the following command as needed:

```jsx
brew services stop mongodb-community@6.0
```

o verify that MongoDB is running, perform one of the following:

- If you started MongoDB **as a macOS service**:

```jsx
brew services list
```

# Run Docker with Podman

Clone the repo

```bash
git clone git@github.com:DeltaX-AI-Lab/models-forge-db.git
```

Log to docker with Podman

```bash
podman login docker.io
```

Pull the latest version of models-forge-db

```bash
podman  pull docker.io/deltaxailab/base:models-forge-db
```

```bash
podman run -d -v /hdd/models-forge-db/:/data/db --restart always --name models-forge -p 27020:27017 docker.io/deltaxailab/base:models-forge-db-v4
```