# RBAC
  - Take me to [Video Tutorial](https://kodekloud.com/topic/role-based-access-controls/)

In this section, we will take a look at RBAC

## How do we create a role?
- Each rule in the k8s `Role` descriptor has 3 sections (sono una lista):
  - apiGroups (per core group, puoi lasciarlo bianco, altrimenti devi specificare l'API Group Name)
  - resources
  - verbs
- create the role with kubectl command
  ```
  $ kubectl create -f developer-role.yaml
  ```
  
- si possono creare anche in maniera imperativa, forse più veloce: 
  ```
  $ kubectl create role <role-name> --verb=<verb1>,<verb2> --resource=<resource1>,<resource2>
  ```

## The next step is to link the user to that role

- For this we create another object called **`RoleBinding`**. This role binding object links a user object to a role.
- create the role binding using kubectl command
  ```
  $ kubectl create -f devuser-developer-binding.yaml
  ```
- Also note that the roles and role bindings fall under the scope of namespace, come vedi, il role binding, lega
lo user al role

- **Se si vuole limitare il namespace in cui quell'utente può avere quel ruolo**, aggiungi il campo namespace ai metadata
del manifest k8s del RoleBindig object

- **Se si vuole limitare la risorsa a cui può accedere, quindi non solo la tipologia, nel Ruolo, utilizza la property 
resourceNames**

  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: developer
  rules:
  - apiGroups: [""] # "" indicates the core API group
    resources: ["pods"]
    verbs: ["get", "list", "update", "delete", "create"]
  - apiGroups: [""]
    resources: ["ConfigMap"]
    verbs: ["create"]
  ```
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: devuser-developer-binding
  subjects:
  - kind: User
    name: dev-user # "name" is case sensitive
    apiGroup: rbac.authorization.k8s.io
  roleRef:
    kind: Role
    name: developer
    apiGroup: rbac.authorization.k8s.io
  ```
  ![rbac1](../../images/rbac1.PNG)
  

- si possono creare i RoleBinding anche in maniera imperativa, forse più veloce: 
  ```
  $ kubectl create rolebinding <rolebinding-name> --role=<verb1>,<verb2> --user=<resource1>,<resource2>
  
## View RBAC
  
- To list roles
  ```
  $ kubectl get roles
  ```
- To list rolebindings
  ```
  $ kubectl get rolebindings
  ```
- To describe role 
  ```
  $ kubectl describe role developer
  ```
  
  ![rbac2](../../images/rbac2.PNG)
    
- To describe rolebinding
  ```
  $ kubectl describe rolebinding devuser-developer-binding
  ```
  
  ![rbac3](../../images/rbac3.PNG)
  
#### What if you being a user would like to see if you have access to a particular resource in the cluster.
## Check Access

- You can use the kubectl auth command
  ```
  $ kubectl auth can-i create deployments
  $ kubectl auth can-i delete nodes
  ```
  ```
  $ kubectl auth can-i create deployments --as dev-user
  $ kubectl auth can-i create pods --as dev-user
  ```
  ```
  $ kubectl auth can-i create pods --as dev-user --namespace test
  ```
  
  ![rbac5](../../images/rbac5.PNG)
  
## Resource Names
- Note on resource names we just saw how you can provide access to users for resources like pods within the namespace,
ossia diciamo che questo ruolo può interagire con i Pod, ma solo con un certo nome



  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: developer
  rules:
  - apiGroups: [""] # "" indicates the core API group
    resources: ["pods"]
    verbs: ["get", "update", "create"]
    resourceNames: ["blue", "orange"]
  ```  
  ![rbac4](../../images/rbac4.PNG)
  
#### K8s Reference Docs
- https://kubernetes.io/docs/reference/access-authn-authz/rbac/
- https://kubernetes.io/docs/reference/access-authn-authz/rbac/#command-line-utilities
