%title: #LNM February 2017 - Node.js and Kubernetes
%author: @lukeb0nd
%date: 2017-02-15

\                   '##::: ##::'#######::'########::'########::::::::::::'##::'######::
                    ###:: ##:'##.... ##: ##.... ##: ##.....::::::::::::: ##:'##... ##:
                    ####: ##: ##:::: ##: ##:::: ##: ##:::::::::::::::::: ##: ##:::..::
                    \## ## ##: ##:::: ##: ##:::: ##: ######:::::::::::::: ##:. ######::
                    ##. ####: ##:::: ##: ##:::: ##: ##...:::::::::'##::: ##::..... ##:
                    ##:. ###: ##:::: ##: ##:::: ##: ##:::::::'###: ##::: ##:'##::: ##:
                    ##::. ##:. #######:: ########:: ########: ###:. ######::. ######::
                   ..::::..:::.......:::........:::........::...:::......::::......:::

-> A N D

'##:::'##:'##::::'##:'########::'########:'########::'##::: ##:'########:'########:'########::'######::
 ##::'##:: ##:::: ##: ##.... ##: ##.....:: ##.... ##: ###:: ##: ##.....::... ##..:: ##.....::'##... ##:
 ##:'##::: ##:::: ##: ##:::: ##: ##::::::: ##:::: ##: ####: ##: ##:::::::::: ##:::: ##::::::: ##:::..::
 #####:::: ##:::: ##: ########:: ######::: ########:: ## ## ##: ######:::::: ##:::: ######:::. ######::
 ##. ##::: ##:::: ##: ##.... ##: ##...:::: ##.. ##::: ##. ####: ##...::::::: ##:::: ##...:::::..... ##:
 ##:. ##:: ##:::: ##: ##:::: ##: ##::::::: ##::. ##:: ##:. ###: ##:::::::::: ##:::: ##:::::::'##::: ##:
 ##::. ##:. #######:: ########:: ########: ##:::. ##: ##::. ##: ########:::: ##:::: ########:. ######::
..::::..:::.......:::........:::........::..:::::..::..::::..::........:::::..:::::........:::......:::


-> #LNM - London Node.js Meetup, February 2017
-> ## Luke Bond
-> ## @lukeb0nd

---

# WHO AM I?

- Developer turned DevOps engineer
- In recent years:
  - Mostly Node.js and Docker
  - Consulting, helping teams release more often with higher quality
- Currently working at the UK Home Office (Kubernetes, Docker, CoreOS, red tape)
- I enjoy demystifying and cutting through jargon to make things clearer
- I like to focus on operational issues around Node.js
  - To help Javascript developers be truly full-stack
- Hobbies include home-brewing and making headings with figlet

---

# WHO IS THIS TALK FOR?

- Those wondering how best to host their apps
  - Developers who may be considering Kubernetes
- Those wondering what this Kubernetes thing is all about
- Those wanting to see what a Kubernetes workflow looks like

-> ## I'm not going to give a thorough run-down of all of Kubernetes,
-> ## but to show you how it feels and tell you why you might want to
-> ## take a look for yourself

-> I will demo most things, and use Node apps for the examples

---

# DO YOU NEED KUBERNETES?

- Running a Kubernetes cluster yourself may not be easy, depending on your
  experience
  - For e.g., administering Etcd requires some advanced experience
- If you have a single service, use a PaaS like Heroku if you can
- If self-hosting, use systemd as your process monitor, run one node process
  per core, a load balancer per host, and put all hosts behind a load balancer
  - See this: https://github.com/lukebond/nodeconf-oslo-20160604
  - And this: https://github.com/lukebond/node-interactive-systemd-workshop
- If your app is more complex, Kubernetes is a good option

---

# OTHER BENEFITS OF KUBERNETES AND CONTAINERISATION

- Containerising your services makes functional and acceptance testing easier
- Containerisation gives you an homogeneous deployment artefact for your apps,
  no matter what they're written in
- The internal load balancing and service discovery K8s gives you would have
  to be hand-rolled
- Kubernetes is highly extensible and you can automate a lot on top of it
  - See this: https://github.com/lukebond/coreos-london-operators-20170124

-> ## Kubernetes gives you the benefits of a process monitor plus easy scaling,
-> ## service discovery, internal load balancing, zero-downtime deployments and
-> ## has persistence primitives; all out of the box

-> ## Think of Kubernetes as a data centre operating system

---

# SO WHY ARE YOU TALKING ABOUT THIS AT A NODE MEETUP?

- Using Kubernetes is no different whether it's running apps in Node or any
  other language
- But there are some reasons why Node developers may find it comfortable and
  complementary:
  - Node is single-process, so Node and containers are a natural fit
  - Many Node developers are used to process managers- forever, PM2, systemd;
    Kubernetes will feel familiar in the way it looks after your processes,
    restarts them, etc.
  - If your app is sufficiently simple, then you may not need Kubernetes
  - If you're doing microservices, then internal load balancing and service
    discovery is something you need to solve as you scale
    - Kubernetes does this for you
    - It's not hard but it's more operational overhead

-> ## In short: if your app is sufficiently complex, Kubernetes is the best
-> ## way to give you as few operational headaches as possible

---

# HOW WOULD USING KUBERNETES IMPACT MY WORKFLOW?

- Depends what you currently have of course!
- I'm assuming you push to Git and have something like Jenkins to run tests
  and build a deployment artefact
- Kubernetes comes after that point in the pipeline, so not much changes

-> ## If you're a dev who doesn't want to get involved in Ops, the main change
-> ## you'll notice is the optional benefits that containers bring to your
-> ## local workflow

---

# KUBERNETES FEATURES: DEPLOYMENTS

- Like all things in Kubernetes, a Deployment is a declarative resource
- You declare what you want the result of the deployment to be, and Kubernetes
  will bring the cluster to that state
- It can be used to upgrade services, including doing rolling upgrades
- I'm only going to cover the most basic of use cases to give you the idea

-> # ¡DEMO 01!

---

# KUBERNETES FEATURES: SERVICE DISCOVERY

- Kubernetes can seamlessly route traffic between services
- No client-side load-balancing required
- It handles services disappearing and being restarted
  - Note that this obviously doesn't mean you'll never have a failed request!
- Routing is by service name (e.g. talk to Redis using e.g. `http://redis`)
- The Kubernetes feature that makes this possible is the "Service". It's like
  a load balancer
- Plugging services together in this manner is very simple and intuitive

---

# KUBERNETES FEATURES: SERVICE DISCOVERY

- Each service gets a virtual, cluster-internal IP address by default
- Yes, this means that services are not exposed outside the cluster
- There are various other ways Services can be exposed
  - Accessible on the same port on every host (which you can then tie to a LB)
  - Kubernetes can configure cloud-provider load balancers
  - Ingress controllers are a new thing that are good for L7 (HTTP) services

-> ## To implement this yourself you would need nginx/HAProxy instances that
-> ## dynamically react to containers starting and stopping.
-> ## Trust me, I've built this before and you don't want to do be doing that!

-> # ¡DEMO 02!

---

# KUBERNETES FEATURES: SECRETS AND CONFIGURATION

- Kubernetes has various ways of getting config into your apps
- There should be enough flexibility to handle even legacy config setups
- For things that are sensitive (e.g. keys, certificates, passwords),
  Kubernetes has the "Secret" resource
- Don't think of the secrets feature as 100% secure no matter what
- The security they offer is currently limited and primitive
  - Understand this and secure them yourself
  - Kubernetes moves fast, new features are UX first, perfection later
  - Access to the Kubernetes API means access to the secrets
  - A process running on the cluster doesn't have access to them (unless you
    don't secure the API)
  - Encryption of secrets is being explored

-> # ¡DEMO!

---

# KUBERNETES FEATURES: PERSISTENCE & DATABASES

A question I often get asked is:

-> ## Should I run databases on Kubernetes?

- We're quickly running out of excuses not to
- You could always run databases in containers using volume mounts and pinning
  containers to hosts
- Running in containers may change the performance profile; for you to test
  for your use case
- Kubernetes now has persistent volume primitives that provide basic support
  for scheduling containers with persistent storage requirements
- A pod declares a claim for a volume and Kubernetes schedules it alongside
  the volume it requested

---







-> # Thanks!!

-> ## Any questions?





-> Slides can be found here: [https://github.com/lukebond/lnm-nodejs-and-kubernetes-talk-20170215](https://github.com/lukebond/lnm-nodejs-and-kubernetes-talk-20170215)
