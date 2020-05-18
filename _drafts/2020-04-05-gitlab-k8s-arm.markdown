---
layout: post
title:  "Gitlab and k8s ARM"
date:   2020-04-05 00:00:00 +0000
categories: gitlab k8s kubernetes arm arm64
---
One of my projects at home is a small [Kubernetes][kubernetes] cluster. I decided to have one here as I previously had
been using [Kubernetes][kubernetes] at work but in my current position they are not using it and I want to keep my
[Kubernetes][kubernetes] knowledge current. A great example of why this is relevant is the recent release of [Kpt][kpt].

At home I already have a few systems running services like [Nextcloud][nextcloud] and [Gitlab][gitlab].
[Gitlab][gitlab] can integrate nicely with [Kubernetes][kubernetes], at least in theory. The first step in this
integration is [Gitlab][gitlab] installing [Helm][helm] on the cluster. Instantly this failed and I figured documenting
the failure and how I figured out what was wrong would be useful. In the below image is the error I got back from
[Gitlab][gitlab].

[Image][image]



Peace, love and happiness.

[kubernetes]: https://kubernetes.io/
[kpt]: https://googlecontainertools.github.io/kpt/
[nextcloud]: https://nextcloud.com/
[gitlab]: https://about.gitlab.com/
[gitlab-helm-image-issue]: https://gitlab.com/gitlab-org/cluster-integration/helm-install-image/-/issues/6