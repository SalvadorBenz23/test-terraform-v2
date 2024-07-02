1.	main.tf:
	-	This file will call the different modules and pass the necessary variables to them.
	
2.	variables.tf:
	-	This file defines all the variables used in the project, including region, VPC CIDR blocks, subnet CIDR blocks, etc.
	
3.	outputs.tf:
	-	This file defines the project outputs, such as subnet IDs and security group IDs.
	
4.	providers.tf:
	-	This file defines the providers and their configurations.

5.	modules/:
	-	This directory contains all the modules, each representing a logical infrastructure component.

6.	terraform.tfvars:
	-	This directory allows centralized control over values defined in the different modules.


here are some logs you might want to reproduce:

ubuntu@ip-10-0-2-96:~$ kubectl get pods -n development
NAME                       READY   STATUS    RESTARTS   AGE
backend-667d846676-7pmjp   1/1     Running   0          39s
backend-667d846676-qtsq7   1/1     Running   0          39s
ubuntu@ip-10-0-2-96:~$ kubectl get secrets -n development
NAME        TYPE     DATA   AGE
test-auth   Opaque   2      92s
ubuntu@ip-10-0-2-96:~$ kubectl get secret test-auth -n development -o jsonpath="{.data.MONGO_INITDB_ROOT_USERNAME}" | base64 --decode
ds_rootubuntu@ip-10-0-2-96:~$
ubuntu@ip-10-0-2-96:~$ kubectl get secret test-auth -n development -o jsonpath="{.data.MONGO_INITDB_ROOT_PASSWORD}" | base64 --decode
ds_root_passwordubuntu@ip-10-0-2-96:~$
ubuntu@ip-10-0-2-96:~$ POD_NAME=$(kubectl get pods -n development -l app=backend -o jsonpath="{.items[0].metadata.name}")
ubuntu@ip-10-0-2-96:~$ kubectl exec -n development $POD_NAME -- printenv | grep MONGO_INITDB
MONGO_INITDB_ROOT_USERNAME=ds_root
MONGO_INITDB_ROOT_PASSWORD=ds_root_password
ubuntu@ip-10-0-2-96:~$ kubectl logs -n development $POD_NAME

> ds_server@1.0.0 start
> node dist/index.js

Server is running at port 3001
connection error 💩: MongoServerError: bad auth : Authentication failed.
    at Connection.sendCommand (/app/node_modules/mongoose/node_modules/mongodb/lib/cmap/connection.js:297:27)
    at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
    at async Connection.command (/app/node_modules/mongoose/node_modules/mongodb/lib/cmap/connection.js:325:26)
    at async executeScram (/app/node_modules/mongoose/node_modules/mongodb/lib/cmap/auth/scram.js:79:22)
    at async ScramSHA1.auth (/app/node_modules/mongoose/node_modules/mongodb/lib/cmap/auth/scram.js:39:16)
    at async performInitialHandshake (/app/node_modules/mongoose/node_modules/mongodb/lib/cmap/connect.js:101:13)
    at async connect (/app/node_modules/mongoose/node_modules/mongodb/lib/cmap/connect.js:19:9) {
  errorResponse: {
    ok: 0,
    errmsg: 'bad auth : Authentication failed.',
    code: 8000,
    codeName: 'AtlasError'
  },
  ok: 0,
  code: 8000,
  codeName: 'AtlasError',
  connectionGeneration: 0,
  [Symbol(errorLabels)]: Set(2) { 'HandshakeError', 'ResetPool' }
}
error seeding database because its not fuckeenn connected or something who knows 💩, this is the error it gave us though have fun tryna decrypt that shit MongooseError: Operation `messages.insertMany()` buffering timed out after 10000ms
    at Timeout.<anonymous> (/app/node_modules/mongoose/lib/drivers/node-mongodb-native/collection.js:185:23)
    at listOnTimeout (node:internal/timers:573:17)
    at process.processTimers (node:internal/timers:514:7) {
  insertedDocs: []
}
ubuntu@ip-10-0-2-96:~$ kubectl exec -it -n development $POD_NAME -- /bin/bash
root@backend-667d846676-7pmjp:/app#
