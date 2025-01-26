# Module Manager `:FOR[]:NEEDS[]` (failed) Proof of Concept

This is a _**FAILED**_ Proof of Concept for using `:FOR[]:NEEDS[]` while patching third-parties add'ons for features introduced by one Add'On (and that not rarely depends on a third one).

Unfortunately, it failed due an excessive trust on the MM Documentation that not only prevented me to identify the code on MM where the (in my view) misbehaviour would be happening, but also leaded me to cook a Test Case where the problem didn't triggered, fooling me into believing I had a valid theory.

The subjacent idea, however, is still valid - but it can't be implemented using stock Module Manager as I was intending. That really sucks.

The aftermath is an [update](https://github.com/sarbian/ModuleManager/wiki/Patch-Ordering/_compare/b97501810baf9625d33f66515fd85b4a00f17564) on the Forum's Module Manager documentation, in the hopes no one falls into this booby trap again.


## Current Work in Progress Proposal

To split the `:FOR` behaviour into two new MM patches (and not only one as previously):

* `:THIS`, a patch for creating "tags" (_modname_s in the documentation) and nothing more.
	+ It will be, still, always "executed" to preserve that part of the `:FOR` semantics.
* `:AT`, a new patch that will be used for ordering the parching.
	+ Implementing this part of the `:FOR` semantics.
* `:FOR` would be left as is forever, and it would be probably be a nice idea to issue a warning (only once) about it, as IMHO this thing should be considered deprecated once `:THIS` and `AT:` is implemented - if it will ever happen, of course...


## References

* Forum
	+ On [TweakScale Thread](https://forum.kerbalspaceprogram.com/topic/179030-ksp-130-tweakscale-under-lisias-management-2488-2024-1117/?do=findComment&comment=3949924)
	+ On [Module Manager's Thread](https://forum.kerbalspaceprogram.com/topic/50533-18x-112x-module-manager-423-july-03th-2023-fireworks-season/page/308/#comment-4438902), with a follow up [here](https://forum.kerbalspaceprogram.com/topic/50533-18x-112x-module-manager-423-july-03th-2023-fireworks-season/page/308/#comment-4438902). 
* On this repo
	+ [Affected Artefacts](./Docs/affected-artefacts.md) - a list of everything I had to fix or update due this fiasco.
