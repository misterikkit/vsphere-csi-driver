# Copyright 2019 The Kubernetes Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

################################################################################
##                               BUILD ARGS                                   ##
################################################################################
# This build arg allows the specification of a custom Golang image.
ARG GOLANG_IMAGE=golang:1.13

# This build arg allows the specification of a custom base image.
ARG BASE_IMAGE=gcr.io/cloud-provider-vsphere/extra/csi-driver-base:v1.0.2-10-ga6fc92a

################################################################################
##                              BUILD STAGE                                   ##
################################################################################
# Build the manager as a statically compiled binary so it has no dependencies
# libc, muscl, etc.
FROM ${GOLANG_IMAGE} as builder

# This build arg is the version to embed in the CSI binary
ARG VERSION=unknown

# This build arg controls the GOPROXY setting
ARG GOPROXY

WORKDIR /build
COPY go.mod go.sum ./
COPY pkg/    pkg/
COPY cmd/    cmd/
ENV CGO_ENABLED=0
ENV GOPROXY ${GOPROXY:-https://proxy.golang.org}
RUN go build -a -ldflags='-w -s -extldflags=static -X sigs.k8s.io/vsphere-csi-driver/pkg/csi/service.version=${VERSION}' -o vsphere-csi ./cmd/vsphere-csi

################################################################################
##                               MAIN STAGE                                   ##
################################################################################
FROM ${BASE_IMAGE}
LABEL "maintainers"="Divyen Patel <divyenp@vmware.com>, Sandeep Pissay Srinivasa Rao <ssrinivas@vmware.com>, Xing Yang <yangxi@vmware.com>"

COPY --from=builder /build/vsphere-csi /bin/vsphere-csi

ENTRYPOINT ["/bin/vsphere-csi"]
