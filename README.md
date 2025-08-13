# Docker & Kubernetes

Esta guía recopila explicaciones, ejemplos y *cheat sheets* para aprender y trabajar con Docker y Kubernetes de forma práctica. Está organizada por temas (desde conceptos básicos, comandos y archivos de configuración, hasta ejemplos reales) y enlaza a recursos externos y cursos gratuitos para profundizar.  

**Instalación - Docker:** 
- Guía oficial de instalación de Docker: [Get Docker](https://docs.docker.com/get-docker/)  
- Si tu distribución de Linux no soporta Docker Desktop, puedes instalar directamente el **Docker Engine** siguiendo la documentación oficial: [Install Docker Engine](https://docs.docker.com/engine/install/)  

> Los [cursos de Youtube](README.md#cursos-online-gratis-youtube-freecodecamp-channel) enlazados en esta guía también incluyen pasos de instalación y configuración.

## Resumen de conceptos principales de Docker y Kubernetes

Una vez lo tengas todo instalado, ¡empieza por aquí!: 
  - [Docker_concepts.md](./Docker_concepts.md)
  - [Kubernetes_concepts.md](./Kubernetes_concepts.md) (¡Pendiente!)

## Cheat Sheets de los principales comandos de Docker

- [Docker_CLI_Cheat_Sheet.md](./Docker_CLI_Cheat_Sheet.md)
- [Docker_Compose_Cheat_Sheet.md](./Docker_Compose_Cheat_Sheet.md) 

## Dockerfile y docker-compose.yml
- Qué es el Dockerfile y cómo crear uno: [Dockerfile_explained.md](./Dockerfile_explained.md)
- Qué es docker-compose.yml y cómo crear uno: [Docker_compose_explained.md](./Docker_compose_explained.md)

### Ejemplos explicados de Dockerfile y docker-compose.yml reales para distintas arquitecturas

- [Dockerfile (Frontend: React+Vite+Nginx)](https://github.com/duquediazn/docker-and-kubernetes/tree/main/Dockerfile%20Examples/React%2BVite%2BNginx)
- [Dockerfile (Backend: FastApi)](https://github.com/duquediazn/docker-and-kubernetes/blob/main/Dockerfile%20Examples/FastApi/Dockerfile)
- [Docker-compose (React+FastAPI+PostgreSQL)](https://github.com/duquediazn/docker-and-kubernetes/blob/main/Docker%20Compose%20Examples/docker-compose.yml)

## Recursos para aprender sobre Docker y Kubernetes.

Aquí se listan todos los recursos empleados para realizar esta guía/tutorial sobre Docker.

### Cursos online gratis (YouTube freeCodeCamp channel)

#### Docker Containers and Kubernetes Fundamentals – Full Hands-On Course
- [Enlace a Youtube](https://www.youtube.com/watch?v=kTp5xUtcalw) 
- Duración: 5:56:36
- Descripción: Este curso hace un recorrido un poco más superficial y menos técnico sobre Docker para después centrarse más en Kubernetes. 
- Recursos: [Repositorio del curso](https://github.com/K8sAcademy/Fundamentals-HandsOn)

#### Docker Tutorial for Beginners - A Full DevOps Course on How to Run Applications in Containers
- [Enlace a Youtube](https://www.youtube.com/watch?v=fqMOX6JJhGo): 
- Duración: 2:10:18 
- Descripción: Este curso sí explica mucho mejor los engranajes de Docker, sin dejar de ser "beginner friendly" (animaciones, analogías, etc.)
- Recursos: [Practice Labs](https://learn.kodekloud.com/courses/udemy-labs-docker-swarm-hands-on-devops)

### Documentación oficial

- [Docker docs](https://docs.docker.com/)
- [Kubernetes docs](https://kubernetes.io/es/docs/concepts/overview/)


