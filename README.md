# Repro for issue 7677

## Versions

firebase-tools: v13.17.0<br>
node: v20.12.1<br>
gcloud:

```
Google Cloud SDK 493.0.0
beta 2024.09.13
bq 2.1.8
cloud-datastore-emulator 2.3.1
cloud-firestore-emulator 1.19.9
core 2024.09.13
gcloud-crc32c 1.0.0
gsutil 5.30
```

## Steps to reproduce

1. Run `npm install`
2. Run `npm run start:emulator:new`
3. Open a new terminal, then run `npm run build && npm run start`
   - Outputs an empty array

```
[]
```

## Notes

### Running using the old emulator

1. Run `npm install`
2. Run `npm run start:emulator:old`
3. Open a new terminal, then run `npm run build && npm run start`
   - Outputs

```
[
  {
    fields: [ [Object] ],
    email: 'foo@example.com',
    [Symbol(KEY)]: Key {
      namespace: undefined,
      id: '13',
      kind: 'User',
      path: [Getter]
    }
  },
  ...
]
```

### Running using a real project

1. Generate a service account key with permission to read//write to Datastore
2. Save the key to a file called `service-account.json`
3. Run `export GOOGLE_APPLICATION_CREDENTIALS=service-account.json`
4. In `src/index.js` replace

```ts
const datastore = new Datastore({
  projectId: "dummy-project-id",
});
```

with

```ts
const datastore = new Datastore({
  projectId: "ACTUAL_PROJECT_ID",
  databaseId: "datastore-db", // If db is non-default
});
```

5. Run `npm run build && node .`
   - Outputs

```
[
  {
    fields: [ [Object] ],
    email: 'foo@example.com',
    [Symbol(KEY)]: Key {
      namespace: undefined,
      id: '5701666712059904',
      kind: 'User',
      path: [Getter]
    }
  }
]
```
