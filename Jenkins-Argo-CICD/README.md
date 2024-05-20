---

![1](https://github.com/AKACHI-4/DevOps/assets/99159580/5c675505-f4e7-4f88-83fc-9b12dc4f3804)

---

<br>

**Interview Question** : what is your real-time ci/cd project in your organization ?

- In ArgoCD part it is **Pull mechanism**.
- Jenkins pipeline ends at tests thus its only doing **Continuous Integration**
- No link attached between **ci** and **cd** part
- Jenkins pipeline no way triggering **Continuous Delivery**

<br>

- **ci** ensures build smooth, text executed, code quality maintained and image created.
- **cd** ensures deployment or delivery process is done, we r using **k8s** here.

<br>

**Interview Question** : a developer committed code into a *git repo* how does **Jenkins** get notified ?

**Ans :** there r multiple ways to get done so : but most efficient one is two use **webhook** - jenkins will not keep on watch over the git but git itself notify to *jenkins* whenever changes made

- *how to configure ?* : *Jenkins* provide webhook url which can be inserted within the git repo settings - we can define for which *action* webhook should be triggered

*webhook* notify Jenkins to **triggers** the pipeline - and *Jenkins* start *triggering* the pipeline - *devops engineer* role starts. - **JenkinsFile** - we can perform set of action on jenkins - what are those set of **actions** ? *(in the perspective of we r on java application)*

- using **maven** we will try with building the application → go to jenkins > go to configure system >  and we install *maven* **or** we can use a **docker agent** (we can skip installation and configuration)
- as part of **build** → unit test will run → some *static code analysis →* we can move to next **stage**
- if anything **fails** above → *Jenkins* provide **email plugin** for alarm → to notify ( we can use anything like *slack notification* through *mail based apis* and anything )
- as part of next *stage* → Integrating with **sonarQube** → or any *code & security scanning* tools → we can verify what happened in *previous stage → build* was successful → did it reach specific *threshold* for organization → Is it matching with *compliance* of organization ? → does it have error less then *x %* ? → are there any *security vulnerabilities* ? if *yes* → then again get *notifies* regarding that.
- if everything fine above → we can proceed with creation of **Docker Image →** again use a **Docker agent** itself ****or we can create a **Docker plugin** here → *or* we can run a **shell script**

<br>

**Interview Question** : what type of **JenkinsFile** are we following ? what agents are we using in ur *JenkinsFile ?*

**Ans** : Always go with **declarative** JenkinsFile, coz they are much better, *whereas* **scripted pipeline** is more of *unstructured way* → only usable in case of *strong dev team* → **declarative** is much more easier to write, understand and collaborate → for people like with weak hold on *groovy scripting, technical understanding* and *programming stuff*.

we are using ***docker agent*** which is very *light-in weight* coz reduces a lot of configuration and installation.

- after running `docker build` we’ll have a docker image which we’ll send to a docker or container registry *(ecr, quay.io, dockerhub)*
- **SAST** or **DAST** Testing ?

all till here nothing but **Continuous Integration**

<br>

after **ci** we’ve an image ready with new tag *(example-app:v1.0.1)* 

once image get pushed how does the **cd** get triggered : **how does *ci* and *cd* communicate with each other ?**

- *traditionally* : **ansible**, **shell scripts** and include **cd** into the same **ci** pipeline : means once the image is built, they trigger the ansible playbook or shell scripts which push or deploy the **artifacts** to **K8s** or any other target platform.
- but the *problem* with this is that **not much scalable** and these are not designed for the **continuous delivery**
- so we have to come to choose a *cd tool* and best tool available in market is **gitops** based tool - why ?
    - *GitOps* practices are similar to source code.
    - we should create a *git repo* which will have *application **manifest***, its nothing but k8s `pod.yaml`, `deploy.yaml` or `service.yaml` *(for example only)*
    - but why we need to put this in *git repo* couldn’t we make these in *ci pipeline* - **actually we don’t have proper mech. if we doing that.**
    - for example if we have to make changes in these manifest, like adding or changing volume, mount point etc. - then ci flow will not come handy - coz ci triggered on source code change but here we are originally changing the `pod.yaml` with no source of truth ( adding more volume to the pod )
    - In these cases if we don’t use the proper *gitops* tools then **do** engineer have to *login in k8s cluster and he’s to directly update the `pod.yaml` - **NO VERIFICATION** - **NO AUDITING** - **NO CODE REVIEW.***
    - Thus, we have to use *git repo* not just for the source code but for *application manifest* too.
    - how *argo cd* or *gitops* tool come to know that there’s update or new version image in ci pipeline - so my `pod.yaml` or `helm-chart` ( whatever it is ) how they’ll come to know that `helm-chart` should be update with a `new version` that was created by the ci pipeline.
    - For that, we can do in *two ways* :
        - **argo image updater :** continuously monitor the *dockerhub* or any of *container registry*, and whenever a new image version is pushed to registry, **it** notify *manifest git repo, with new version in manifests, if helm-chart then it could be updated too.*
        - then there’s **argo cd :** continuously watching manifest repo and it says that whenever change happens, take the **new manifests**, and deploy the k8s cluster.
    - **argo image updater** achieves this by committing the registry changes in *manifests repo*
    - as soon as the commit is created; *what is the advantages of gitops tools ?*
        - *gitops tools : which r **k8s controller***, basically sitting inside the ***k8s cluster** : **argo cd*** : always maintains state between *manifests repo* & *k8s cluster*
    
    and this is complete **cd** process.
    
<br>

**implementation part :** if something important tho

- can be `jenkins file` could be anywhere in scm with any name : **yes.**
- using the `docker agent` achieve minimal configuration in `jenkins pipeline` :
    - install `docker agent` plugin and choose `docker image` wisely.
    - most of the time `do engineer` have to write this `image` file.
    - **jenkins** responsibility to run or trigger docker container using the *image*
- managing **sonar plugin** in jenkins server too.
- *public ips* for demo in orgs we use **private ips**
- whats the difference between **adduser** & **useradd ?**
