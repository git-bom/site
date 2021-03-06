+++
title = "GitBOM"
toc=true
+++

An [Artifact Dependency Graph (ADG)](/glossary/artifact_dependency_graph) can be represented as a directed acyclic graph(DAG) with the nodes identified by an [artifact id](/glossary/artifact#artifact-identifiers).

```mermaid
flowchart BT
    Artifact-2[Artifact-2 ID] --> Artifact-1[Artifact-1 ID]
    Artifact-3[Artifact-3 ID] --> Artifact-1[Artifact-1 ID]
    Artifact-4[Artifact-4 ID] --> Artifact-2[Artifact-2 ID]
    Artifact-5[Artifact-5 ID] --> Artifact-2[Artifact-2 ID]
    Artifact-6[Artifact-6 ID] --> Artifact-3[Artifact-3 ID]
    Artifact-7[Artifact-7 ID] --> Artifact-3[Artifact-3 ID]
```

GitBOM advocates for using the [gitoid](/glossary/git/#git-object-id-gitoid) of an artifact as its [artifact id](/glossary/artifact#artifact-identifiers):

```mermaid
flowchart BT
    Artifact-2[Artifact-2 gitoid] --> Artifact-1[Artifact-1 gitoid]
    Artifact-3[Artifact-3 gitoid] --> Artifact-1[Artifact-1 gitoid]
    Artifact-4[Artifact-4 gitoid] --> Artifact-2[Artifact-2 gitoid]
    Artifact-5[Artifact-5 gitoid] --> Artifact-2[Artifact-2 gitoid]
    Artifact-6[Artifact-6 gitoid] --> Artifact-3[Artifact-3 gitoid]
    Artifact-7[Artifact-7 gitoid] --> Artifact-3[Artifact-3 gitoid]
```

## GitBOM Document
The parent-child relationship is captured by a set of GitBOM Documents.

Each artifact has a GitBOM document that describes its immediate children consiting of a set of new line delimited records, one for each child, in lexical order.

A child artifact which is itself a [leaf artifacts](/glossary/artifact/#leaf-artifacts) would be represented by

```
blob⎵${gitoid of child}\n
```

A child artifact which is itself a [derived artifact](/glossary/artifact/#derived-artifacts) would be represented by
```
blob⎵${gitoid of child}⎵bom⎵${gitoid of child's GitBOM document}\n
```

Example:

```mermaid
flowchart BT
    Artifact-2[Artifact-2 gitoid] --> Artifact-1[Artifact-1 gitoid]
    Artifact-3[Artifact-3 gitoid] --> Artifact-1[Artifact-1 gitoid]
    Artifact-4[Artifact-4 gitoid] --> Artifact-2[Artifact-2 gitoid]
    Artifact-5[Artifact-5 gitoid] --> Artifact-2[Artifact-2 gitoid]
    Artifact-6[Artifact-6 gitoid] --> Artifact-3[Artifact-3 gitoid]
    Artifact-7[Artifact-7 gitoid] --> Artifact-3[Artifact-3 gitoid]
```

Artifact-2's GitBOM:

```
blob⎵${gitoid of Artifact-4}\n
blob⎵${gitoid of Artifact-5}\n
```

Artifact-3's GitBOM:
```
blob⎵${gitoid of Artifact-6}\n
blob⎵${gitoid of Artifact-7}\n
```

Artifact-1's GitBOM:
```
blob⎵${gitoid of Artifact-2}⎵bom⎵${gitoid of Artifact-2's GitBOM}\n
blob⎵${gitoid of Artifact-3}⎵bom⎵${gitoid of Artifact-2's GitBOM}\n
```

## GitBOM Identifier

For a given artifact, the GitBOM ID of that artifact is simply the gitoid of its corresponding GitBOM Document.

## GitBOM Identifier Embedding

GitBOM advocates for [build tools](/resources/glossarybuildtool) to embed into each [derived artifact](/glossary/artifact/#derived-artifacts) they are building that derived artifact's GitBOM ID.

Examples:

**ELF Files (Executables and .so, and .o files)**
: Embed GitBOM identifier into an elf section named ‘.bom’

**ar Files (.a static libraries)**
: Embed GitBOM identifier into an archive entry named ‘.bom’

**General Archive files (tar,gzip,etc)**
: Embed GitBOM identifier into an archive entry named ‘.bom’

**Java class file**
: Embed GitBOM identifier into an annotation named @BOM in the .class file.

**Python .pyc files**
: Embed GitBOM identifier into an __bom__ in the .pyc file.

**Container Images**
: Embed GitBOM identifier into the image manifest as an annotation named “dot.bom”

**Generated Source Code**
: Embed GitBOM identifier for a generated source code file using a comment

## GitBOM Compliments SBOM
GitBOM can help [SBOMs](/glossary/sbom) be more precise and reliable.

See [SBOM: Gitbom Compliments SBOMs](/glossary/sbom/#gitbom-compliments-sbom) for further details.
