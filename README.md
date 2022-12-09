# The Example System Archtecture

Laravel, Nuxt.js, MySQL, Redis, Kubernetes, on AWS Edition

The business problem/roadmap item is the need for a document and task manager. The following is what we know so far: -The system shall allow a user to add documents

- The system shall version documents
- The system shall allow a user to add tasks to a document
- The system shall allow a user to download a document

Considerations:

- UI currently with design
- Product started user stories but needs to ensure technical solution before continuing
- Solution needs to consider how integrations with client's document management system will be impacted/ handled.
- This roadmap item is critical to a new client launch, which is expected to be completed by October.

Existing technology/infrastructure stack/skill of developers: 

- BE API (PHP / Laravel)
- FE (Nuxt / Vue.js)
- DB (MYSQL, REDIS)
- CLOUD (AWS, Kubernetes)
- CI/CD (Github actions / ArgoCD)

Please document what questions you have, assumptions you would make when coding, technical dependencies and draft a handful of technical stories. Along with the stories, provide a magnitude of effort so we can understand what we can do as a minimum for the October release. And lastly, a diagram if applicable to assist in explaining the above.

# Supporting Research

Before trying to connect different tools and technologies together, it is important to understand how they both function and can integrate. It is for that reason I like to generally standup small and self-contained example repositories for getting a better understanding. In this case, the following repositories represent each individual technology consideration:

- PHP / Laravel, which includes MySQL and Redis: https://github.com/jvalentino/example-docker-laravel
- Nuxt.js: https://github.com/jvalentino/example-nuxtjs
- Kuberentes: https://github.com/jvalentino/setup-kubernetes
- ArgoCD: https://github.com/jvalentino/setup-argocd

Consider that I build such examples based on other research and supporting projects that already exist, which in this case would be:

- Docker and Docker Compose: https://github.com/jvalentino/setup-docker
- Git: https://github.com/jvalentino/setup-git









