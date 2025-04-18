### PersistentVolumes and PersistentVolumesClaims

![image](https://github.com/user-attachments/assets/ceb2df6c-2d13-4e87-93a8-2417a5b6a063)

 #### Key Components in the Diagram:
 - Node: A single machine (either physical or virtual) in the Kubernetes cluster.
 - Pod1 & Pod2: These are two pods running on the node. Pods are the smallest deployable units in Kubernetes.
 - PVC (Persistent Volume Claim): A request made by a pod to use storage.
 - PV (Persistent Volume): The actual storage resource in the cluster, backed by physical or cloud storage (e.g., a disk on the host or cloud volume).
 - Host or Server (Storage 10GB): This is the underlying server with 10GB of physical storage available.

#### Flow Explanation:
- Storage Exists on the Host:
  - A server/host in the cluster has 10GB of available storage.
- Persistent Volume (PV):
    - An admin creates a PV from the host storage.
    - For example, in this diagram, a PV of 5GB is created (out of 10GB total).
- Persistent Volume Claim (PVC):
  - One or more pods (Pod1 and Pod2 here) request storage by creating a PVC.
  - PVC is essentially a user’s request for a certain amount of storage (e.g., 5GB).
  - Kubernetes checks if there’s a PV that satisfies the PVC’s request.
- Binding PVC to PV:
  - PVC gets bound to an available PV (in this case, the 5GB PV).
  - Once bound, the storage is now available for use by the pod(s).
- Access by Pods:
  - Both Pod1 and Pod2 are accessing the same PVC, which means they are sharing the 5GB PV.
  - This is possible if the access mode of the volume supports it, such as ``` ReadWriteMany ```.
 


![image](https://github.com/user-attachments/assets/435ed1ac-20cf-440b-a02d-abcec95f19de)

### Persistent Volume is a manifest that request a persistent volume storage

### Summary:
- PV is storage provisioned by the admin.
- PVC is a request for that storage by a user or application.
- Kubernetes binds a PVC to a matching PV.
- Multiple pods can use the same PVC if access modes and policies allow.

