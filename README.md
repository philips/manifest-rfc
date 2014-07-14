## Container manifest

The YAML file is formatted as follows:

```
version: v1beta1      // Required.
containers:           // Required.
  - name: string      // Required.
    image: string     // Required.
    command: [string]
    workingDir: string
    volumeMounts:
      - name: string
        path: string
        readOnly: boolean
    ports:
      - name: string
        containerPort: int
        hostPort: int
        protocol: string
    env:
      - key: string
        value: string
volumes:
  - name: string
```

|Field name				|Value type	|Required?	|Spec|
|---------------------------------------|---------------|---------------|----|
|version				|string		|Required	|The version of the manifest. Must be v1beta1.|
|containers[]				|list		|Required	|The list of containers to launch.|
|containers[].name			|string		|Required	|A symbolic name used to create and track the container. Must be an RFC1035 compatible value (a single segment of a DNS name). All containers must have unique names.|
|containers[].image			|string		|Required	|The container image to run.|
|containers[].command[]			|list of string	|		|The command line to run. If this is omitted, the container is assumed to have a command embedded in it.|
|containers[].workingDir		|string		|		|The initial working directory for the command. Default is the container’s embedded working directory or else the Docker default.|
|containers[].volumeMounts[]		|list		|		|Data volumes to expose into the container.|
|containers[].volumeMounts[].name	|string		|		|The name of the volume to mount. This must match the name of a volume defined in volumes[].|
|containers[].volumeMounts[].path	|string		|		|The path at which to mount the volume inside the container. This must be an absolute path and no longer than 512 characters.|
|containers[].volumeMounts[].readOnly	|boolean	|		|Whether this volume should be read-only. Default is false (read-write).|
|containers[].ports[]			|list		|		|Ports to expose from the container. All of these are exposed out through the public interface of the VM.|
|containers[].ports[].name		|string		|		|A symbolic name used to create and track the port. Must be an RFC1035 compatible value (a single segment of a DNS name).|
|containers[].ports[].containerPort	|int		|		|The port on which the container is listening.|
|containers[].ports[].hostPort		|int		|		|The port on the host which maps to the containerPort. Default is the same as containerPort.|
|containers[].ports[].protocol		|string		|		|The protocol for this port. Valid options are TCP and UDP. Default is TCP.|
|containers[].env[]			|list		|		|Environment variables to set before the container runs.|
|containers[].env[].key			|string		|		|The name of the environment variable.|
|containers[].env[].value		|string		|		|The value of the environment variable.|
|volumes[]				|list		|		|A list of volumes to share between containers.|
|volumes[].name				|string		|		|The name of the volume. Must be an RFC1035 compatible value (a single segment of a DNS name). All volumes must have unique names. These are referenced by containers[].volumeMounts[].name.|

### Example manifest file

The file below:

- Creates three containers: the google/docker-registry that allows you to push/pull images from Google Cloud Storage, and a client and a server image pulled from that registry.
- Creates a Docker data volume called data.
- Mounts data on two of the containers.
- Opens up ports on two containers.
- Specifies environment variables on the repository container to set before the container is run.

```
version: v1beta1
containers:
  - name: repository
    image: google/docker-registry
    ports:
      - name: registry
        containerPort: 5000
    env:
      - key: GCS_BUCKET
        value: my-private-repo-bucket
  - name: client
    image: localhost:5000/data-loader
    volumeMounts:
      - name: data
        path: /mnt/data
  - name: server
    image: localhost:5000/data-server
    ports:
      - name: www
        containerPort: 80
    volumeMounts:
      - name: data
        path: /mnt/data
volumes:
  - name: data
```
