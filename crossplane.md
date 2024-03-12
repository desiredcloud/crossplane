
# CROSSPLANE - Cloud Control Plane and more

- Is a distributed systems, with:
  - Control Plane
  - Data Planes
    
![img.png](static/img.png) 

![img.png](static/img1.png)

![img.png](static/img2.png)

- It protects `data plane` using distributed systems best practices:
  - Rate limiting
  - Retries
  - Optimistic concurrency
  
- All Crossplane controllers emit `Prometheus metrics` and k8s events.

---

## Provider

- [ref](https://docs.crossplane.io/latest/concepts/providers/)
- Extends Control plane by adding new API endpoints that represent a related set of cloud primitives.
- These managed APIs are called `ManagedResources(MR)`
- A provider installs controllers that reconcile the desired state of its MRs with the data plane.
- Examples:
  - `provider-aws`, `provider-azure`, `provider-gcp`, `provider-kubernetes`, `provider-terraform`, `provider-sql`
    
## Composition

Most control plane consumers(e.g. Platform Engineers) won’t interact with MRs directly.
Instead, they interact with higher-level API abstractions. When a
consumer(e.g. Application Developer) calls these higher-level APIs, Crossplane reconciles their
desired state into one or more MRs. 
Crossplane calls this composition: the higher-level APIs are composed of one or more MRs.

- In k8s analogy, think `Pod` as `MR` and `Deployment` as `Composition`

## Composite Resource Claims or Claims

Are higher level API abstractions that consumers call to specify the desired state of their data plane.

- A claim is composed of one or more MRs, which is the source of terms like `composite resource` and `composition`.

- Provider authors set MR types and schemes
- ControlPlane curators set claim types and schemas and could be of any shape curators need.

## Composite Resource Definition (XRD)

- `XRD`, lets you define a new kind of claim (special kind of API that adds new APIs to Crossplane)
- Claim:
  - A single `claim` could have group of `MR`s representing several cloud primitives into a single API.
  - For example, a SimplePostgres claim cloud be composed of an RDS-instance MR, a firewall-rule MR, and an IAM role-binding MR.
- XRD also have `kind`(type of your new claim) and `spec`(schema of this new claim)
- When you create an XRD, Crossplane adds a new API endpoint for your kind of claim.
- It also starts a controller that reconciles claims, called a claim controller.
- Claim controller doesn't create MRs directly.
- Between a claim and its MRs lies an intermediate API resource called a composite resource, or `XR`.
- When claim is created, Crossplane automatically creates a matching XR.
- The XR controller then creates MRs.

![img.png](static/img3.png)

*A composite resource definition defines the type and
schema of a claim and a composite resource.*

- Most XRDs define both an XR and a claim.
- All claims have a corresponding XR type, but not all XRs have a corresponding claim type.
- `You can create an XRD that defines only an XR, not a caim`.
  - Allows yu to define private API abstraction: once that control plane consumers can't use directly. 