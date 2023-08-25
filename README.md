# DUO and Derived Annotations

## Abbreviations

- ABAC: Attribute Based Access Control
- DCC: Data Coordinating Center
- DUO: Data Use Ontology
- NF-OSI (also just NF): Neurofibromatosis-focused community and Sage science team

## Intro 

This aims to provide a useful interpretation and synthesis of the **DUO and derived-annotations innovations** from the **governance** and **platform teams**, respectively. We want to help other teams better understand, take advantage of, and guide these innovations in future development. Our perspective is that of a science team that has invested in trying to understand and test out these ideas. And there have been many learnings! But it is clear that we do need more involvement from the science teams, design team, and D&T team before these ideas can reach their fullest potential.

The audience can be anyone at Sage who wants to better understand the good ideas but also limitations behind DUO and derived annotations as they currently exist. New things often require the right background and extra effort/experimentation, which presents a barrier to adoption; hopefully this will help lower the initial barrier of understanding and entry. Though this tries to be an useful take from our experience thus far, it is still only one take from one team, it does not promise to provide complete "coverage", and having other DCC-supporting teams engage and do their own take is best! 

What we have here:

- Documentation and discussion of what is really useful behind DUO and derived annotations, great use cases
- On the other hand, what are the complexities and challenges, basically our learnings on why some things are hard to make work properly (and we certainly don't have everything figured out!)
- Walk-through demo (see tutorial_demo.md) to make these discussions more concrete


## DUO and DUO-plus for Dummies

> Feel free to skip this section if you are familiar with DUO and DUO-plus.

### Basic DUO 

First, though [Data Use Ontology (DUO)](https://www.ebi.ac.uk/ols/ontologies/duo) may sound somewhat confusing to the un-initiated (what's an ontology?), it's helpful to think of DUO as just a framework for data governance, where we're just trying to use the same terminology for data governance-relevant concepts like "data use conditions". With DUO, someone has already defined and classified the different data use conditions that are often relevant to biological data. So one example condition is that in order to get to use a dataset you need IRB approval. Let's code this condition as a tag called "IRB" for now.

### Extending DUO to DUO-plus

But what if we have other conditions that we want standardized concepts for? For example, a less draconian condition than the IRB one is that you agree to acknowledge the authors to get to access to this data. This is where Sage can simply extend the DUO framework, call it DUO-plus, and add governance concepts that we care about. Let's code this condition as a tag called "ACK" for now. 

### Application of DUO and DUO-plus on Synapse

#### First intuition

How can this governance metadata be applied on Synapse and, you know, be useful?  
So on Synapse, a file has various attributes like `fileType`. The structured metadata for an example file is: 

```
json

```

So maybe the initial idea is to attach these conditions in a list, something like?

```
json

```

#### Actual implementation + derived annotations

In actuality, the technical specs that platform has defined* would have each condition be represented as distinct attributes with `TRUE` or `FALSE` values to enable something called derived annotations. 

That means metadata on a file would look like this:

```
json

```

We can now compute (or derive, hence the term "derived annotations") additional metadata on this file. 

The rules for deriving metadata have to be in a JSON schema. A simple schema for deriving that if the geographic country code is US, then jurisdiction = HIPAA, vs an EU country code with jurisdiction = GDPR:

```
json

```

More than materalization (which is what this is) of simple new values, potentially we can also apply access controls dynamically based on attributes. That is, **we can match the file to the right access requirement id based on its attributes**.

*See the original internal tech specs here.


### The good ideas behind DUO/DUO-plus and derived annotations

#### Attribute Based Access Control (ABAC)

What has been described above with DUO represents something closer to [Attribute Based Access Control (ABAC)](https://csrc.nist.gov/Projects/attribute-based-access-control), which would be really powerful. For example, the NIST reference describes that one benefit is "more dynamic access control capability as access decisions can change between requests when attribute values change".

<...more>

#### More understandable and interoperable governance

By using DUO, we align with other repositories that use DUO and this should make it easier to potentially compare or exchange data. 
Distilling governance language into standard attributes also makes it easier to understand the governance aspects of the data, both for machines and people.  

#### Transparency and findability

By having governance metadata be explicit on the assets, users can better search for data and see what to expect. For example, it can be really helpful to be able to filter for data that *does* allow commercial use or *does not* need an IRB. Otherwise, it is a matter of having to read through the project docs or trying to download a file before it's clear what the terms and conditions are.

<...more>

#### Derived annotations is great for other use cases, not just governance

<...more>

### The complexities and hard stuff

#### Nuances of governance scoping and current lack of standardization

Governance is complicated, and DUO-plus inherits complicated governance context.

Think about laws and regulations at the federal, state, and township level. 
There are some laws and regulations (rules) already determined at the federal level that states do not get to rewrite, and others where states *do* have some self-determination.

This is the same with a DCC project, where the analogy is: 
- federal = rules for data determined on a global level, such as how HIPPA or GDPR jurisdiction is determined by geographic origin.
- state =  rules for data determined at the level of the funding agency, suh as where for one agency the funded data may have restrictions on commercial use, and for another there is no such restriction.
- township = rules negotiated on a project-by-project basis with the institutions/PIs, such as specific acknowledgments or IRB requirements.  

The "global rules" may also be called "platform-scoped rules". 

These rules should be maintained by the governance team, who keeps abreast of laws and regulations and are also the experts of what are general governance conditions should be applied. For example, aside from the HIPAA/GDPR example, perhaps a general rule that could be applied across Synapse is the classification/labeling of data into "sensitive" categories based on the data type and species.

To do that, we would need to register a central and reusable governance schema registry that each DCC can reference. 
Each DCC project can import these rules (which are subschemas) as needed when formulating the final schema for a dataset.
Adopting this governance schema registry can help keep governance metadata consistent accross the platform.

**However, the major problems for these "reusable" schemas to work are:**
1. All DCCs on the platform would need to be using the same attributes and values, which is currently not the case.
2. We need more people with understanding of JSON schemas, which is not the case. But the real problem may be that JSON schema is not the right language.

#### Challenge of getting and applying metadata

It is difficult to get all the metadata that we'd want and apply them. While the metadata on the files would allow more scalable governance and fine-grained access control, the process of transferring metadata is definitely burdensome. 

#### JSON schema may be a hurdle

As shown, we have to make use of JSON schema. However, JSON schema is perhaps not right for several reasons:
- It is very verbose, and from our experience many colleagues would have trouble using it to compose rules. 
- It is not expressive enough for complicated situations, and governance often has complicated situations. 
- 

### Other Thoughts

