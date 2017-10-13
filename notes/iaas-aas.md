---
title: Infrastructure-as-a-Service as a Service
sansfont: DejaVu Sans
slide-numbers: true
biblatex: true
biblatex-chicago: true
biblatexoptions: [notes, noibid]
bibliography: ../sources.bib
links-as-notes: true
---

# Building things \newline
that build things \newline
for people who build things \newline
to build things with

## Definition: infrastructure\footnote{\cite{oed:infrastructure}}

1. \alert{noun} The basic physical and organizational structures and facilities
   [...] needed for the operation of a society or enterprise.

## Definition: service\footnote{\cite{oed:service}}

1. \alert{mass noun} The action of *helping* or doing work for *someone*.
    1. \alert{count noun} An act of assistance.

## Infrastructure

\begin{center}
\includegraphics[width=5\TPHorizModule]{../media/iaas-service.png}
\end{center}

## Infrastructure

\begin{center}
\includegraphics[width=5\TPHorizModule]{../media/iaas-users.png}
\end{center}

## Infrastructure as a service

\begin{center}
\includegraphics[width=5\TPHorizModule]{../media/iaas-ops.png}
\end{center}

## Infrastructure as a service

\begin{center}
\includegraphics[width=5\TPHorizModule]{../media/iaas-sad.png}
\end{center}

## Infrastructure-as-a-service as a service

\begin{center}
\includegraphics[width=5\TPHorizModule]{../media/iaas-aas.png}
\end{center}

## Infrastructure as code

Application is executable + environment

- Version control environment definition
- Automate environment creation
- Test environment at deploy

## Pipeline environment configuration

\tiny

```json
{
  "apiVersion": "v1",
  "kind": "Template",
  "metadata": {
    "creationTimestamp": null,
    "name": "hybris"
  },
  "objects": [
    {
      "kind": "ImageStream",
      "apiVersion": "v1",
      "metadata": {
        "name": "${NAME}-runtime",
        "labels": {
          "app": "hybris",
          "role": "jenkins-slave"
        },
        "annotations": {
          "description": "Hybris application image. This image is the production image."
        }
      }
    },
    ... 740 more lines
```

## Pipeline configuration

\tiny

```js
node() {
    checkout scm

    /** Stash doesn't include empty folders (.gitignore gets filtered out) **/
    sh '''
        zip -r 'hybris-custom.zip' 'hybris/bin/custom'
    '''

    /** Stash stuff we need on hybris5-builder nodes **/
    stash(name: 'bilkadk-src-zip', includes: 'hybris-custom.zip')
    stash(name: 'hybris-config', includes: 'hybris/config/**')
    stash(name: 'bilkadk-tools', includes: 'tools/**,build.xml')
    stash(name: 'bilkadk-ENV', includes: 'ENV/**')
    stash(name: 'bilkadk-selenium', includes: 'hybris/bin/custom/bilkadk-acceptance/**')

    /** Openshift uses local.properties from ENV **/
    sh '''
        cp 'ENV/local_openshift.properties' 'hybris/config/local.properties'
    '''
}
... 500 more lines
```

## Obstacles

> - Secret secrets
>     - Nothing to be done
> - Service maintenance
>     - Education; "you just have to\ldots"
>     - Ownership; forced if necessary
> - Service-service maintenance
>     - Coordinate migration window
>         - Ops: provide window
>         - Dev: migrate
>     - Staggered rollout
> - Competing stakeholders

## Summary

Remembering to work *with* each other, not *next to* each other
