# Module Manager `:FOR[]:NEEDS[]` Proof of Concept

This is a Proof of Concept for using `:FOR[]:NEEDS[]` while patching third-parties add'ons for features introduced by one Add'On (and that not rarely depends on a third one).

## Introduction

One increasingly common Use Case on MM patching nowadays is Triangular Dependency.

An Add'On, that depends on some other, is in need to patch a third Add'On to have something accomplished.

[TweakScale](https://forum.kerbalspaceprogram.com/index.php?/topic/179030-ksp-141-tweakscale-under-lisias-management-24321-2020-00804/) and the [Companion Program](https://forum.kerbalspaceprogram.com/index.php?/topic/192216-tweakscale-companion-program-2020-0824/) have this problem, currently tackled down in a pretty blunt but effective way.

The past practice, besides a bit harsh, was effective: shove your patches with triangular dependencies on `:FINAL` and call it a day. To tell you the true, for some borderline situations (as [All Tweak](https://forum.kerbalspaceprogram.com/index.php?/topic/182700-*)), this appears to be the only safe choice at this moment.

However, this leaded to a "Race to the `:FINAL`", where everybody with a dependency problem solved it by shoving their patches there - what rendered the tool useless: it became just another "LEGACY" phase where everybody patches things unchecked. And since `:FINAL` is ideally meant to be used by end-users, a different approach is desirable - and an attempt was made recently by MM by adding `:LAST[]` to the toolchain.

But `:LAST[]` does not really solves **this** Use Case, it's just a `:FINAL` a bit less final - things would be less worse as we would have many small Races to the `:LAST[xxxx]` instead of a big Race to `:FINAL`, but some pretty famous and used Add'Ons will face this problem nevertheless.

> One side does not fits all.

The current "Best Practice" being pushed by some Authors, `:FOR[zzzSomething]` in an attempt to ensure being patched by last without inducing a Race to the Bottom on the `:LAST[]`, does not rally solve the triangular dependency neither - it ends up only making **really hard** for third parties to apply their patches for them. In essence, they solve the problem for them at the expense of everybody else.

What they failed to understand (on an apparent example of [Survivorship Bias](https://en.wikipedia.org/wiki/Survivorship_bias)) on Triangular Dependencies is that most of the time, you don't need to be last one patching a third-party Add'On. You only need, some times, to be the last one applying **your** patches to everybody else.

Currently, the only real solution for this is `:FINAL`, with `:FOR[zzzSomething]` having some effectiveness on less disputed situations.

But there's one Use Case on triangular dependencies, happily the most common one, where a different approach can be useful: when you only need to apply your change to an Add'On when some dependencies are met.

You don't need to be the last one applying this change. You only need to apply your change on a well determined phase of the patching to avoid stomping toes on people that wants to change **your** way of doing things.

The `:FOR[]:NEEDS[]` is the answer for this problem: you apply your patch `:FOR[]` the target Add'On **only** if it's present (as well your dependencies) on `:NEEDS[]`, and anybody else willing to change what you did would, then, apply `:AFTER[]:NEEDS[]` or eventually `:LAST[]:NEEDS[]` on the target Add'On - using **you** as the dependency. 

However, in order to achieve this, we need to make sure we can use `:FOR[]:NEEDS[]` when you are not the one specified by the `:FOR[]`, a ordering directive that (weirdly) differs from `:BEFORE[]`, `:AFTER[]` and `:LAST[]` : it **creates** a tag on the MM tag list (the data structure used by MM to determine when an Add'On is installed) at the same time it ensures the order of the patch - and this is the reason people, usually, pushes `:FOR[]` to be used only by the Add'On author.

**BUT**, when used together `:NEEDS[]`, we can trick MM into behaving in a way when `:FOR[]` will only order the patching, without declaring the presence of the target Add'On.

And that suits our needs.

On a side note, [I advocate](https://forum.kerbalspaceprogram.com/index.php?/topic/179030-ksp-141-tweakscale-under-lisias-management-24321-2020-00804/&do=findComment&comment=3827049) the creation of a directive called `:THIS[]`, that would be the only one declaring presence of Add'Ons (without ordering the patch), waving this feature from `:FOR[]` - this would make things way easier, besides being not exactly necessary as we can almost simulate `:THIS[foobar]` with `:FOR[foobar]` (besides forcing the ordering), and the "new" proposed behaviour for `:FOR[]` can be simulated with `:FOR[foobar]:NEEDS[foobar]`.

In a way or another, my solution for the triangular dependencies relies on `:FOR[target]:NEEDS[target,dependency,yourself]` to work as I expect - something constantly (and wrongly) disputed by a lot of Add'On Authors.

This P.H.D. Thesis :P aims to prove, definitively, that it works as I say it does.


## The Experiment

Three minimalistic *"mods"* where create to demonstrate the mechanism:

* `ModA`, simulating the target add'on in need to be patched
* `ModB`, simulating the soft dependency for `ModA`, but hard dependency for `ModC`
* `ModC`, the add'on that wants to patch `ModA` if and only if `ModB` is present.

The experiment consists on two test runs:

* **With** `ModB` present;
* **Without** `ModB` present.

And the experiment was run on two different KSP versions:

* KSP 1.2.2, using [KSPe](https://github.com/net-lisias-ksp/KSPAPIExtensions) and a special alpha version of [MM /L Experimental](https://github.com/net-lisias-ksp/ModuleManager)
	+ Demonstrates that MM can be back ported down to at least 1.2.2 when some good will and smart coding are available.
* KSP 1.3.1, using [KSPe](https://github.com/net-lisias-ksp/KSPAPIExtensions) and [MM /L Experimental](https://github.com/net-lisias-ksp/ModuleManager).
* KSP 1.10.1, using **only** ["Official" MM](https://forum.kerbalspaceprogram.com/index.php?/topic/50533-*).

Further testing on different KSP versions was considered unneeded.

### Evidences

The evidences for the test runs on my rig can be found below:

* KSP 1.2.2
	+ [With `ModB`](./Evidences/1.2.2/0-With-ModB)
	+ [Without `ModB`](./Evidences/1.2.2/1-Without-ModB)
* KSP 1.3.1
	+ [With `ModB`](./Evidences/1.3.1/0-With-ModB)
	+ [Without `ModB`](./Evidences/1.3.1/1-Without-ModB)
* KSP 1.10.1
	+ [With `ModB`](./Evidences/1.10.1/0-With-ModB)
	+ [Without `ModB`](./Evidences/1.10.1/1-Without-ModB)

The *"source code"* for the stunt is [here](./GameData).

### How to Reproduce

Shove the ["*source*"](./GameData) into your GameData and fire up KSP. :)

I recommend a completely empty GameData (but `Squad`) to make things easier and faster - testing on heavily modded KSPs will not test this thing, will only test Module Manager itself (that works!), but will not hurt. ;)

Check the MM Patching log for the following sequence of patching:

```
[LOG 05:59:12.476] Extracting patches
[LOG 05:59:12.626] Applying patches
[LOG 05:59:12.628] :INSERT (initial) pass
[LOG 05:59:12.737] :FIRST pass
[LOG 05:59:12.737] :LEGACY (default) pass
[LOG 05:59:12.752] :BEFORE[ASSEMBLY-CSHARP] pass
[LOG 05:59:12.752] :FOR[ASSEMBLY-CSHARP] pass
[LOG 05:59:12.752] :AFTER[ASSEMBLY-CSHARP] pass
[LOG 05:59:12.752] :BEFORE[KSPSTEAMCTRLR] pass
[LOG 05:59:12.752] :FOR[KSPSTEAMCTRLR] pass
[LOG 05:59:12.752] :AFTER[KSPSTEAMCTRLR] pass
[LOG 05:59:12.752] :BEFORE[MODA] pass
[LOG 05:59:12.752] :FOR[MODA] pass
[LOG 05:59:12.754] Applying update ModC/config/@PART[ModA-Part]:FOR[ModA]:NEEDS[ModA,ModB,ModC] to ModA/config.cfg/PART[ModA-Part]
[LOG 05:59:12.767] :AFTER[MODA] pass
[LOG 05:59:12.767] :BEFORE[MODB] pass
[LOG 05:59:12.767] :FOR[MODB] pass
[LOG 05:59:12.767] :AFTER[MODB] pass
[LOG 05:59:12.767] :BEFORE[MODC] pass
[LOG 05:59:12.767] :FOR[MODC] pass
[LOG 05:59:12.767] :AFTER[MODC] pass
[LOG 05:59:12.767] :BEFORE[MODULEMANAGER] pass
[LOG 05:59:12.767] :FOR[MODULEMANAGER] pass
[LOG 05:59:12.767] :AFTER[MODULEMANAGER] pass
[LOG 05:59:12.767] :BEFORE[SQUAD] pass
[LOG 05:59:12.767] :FOR[SQUAD] pass
[LOG 05:59:12.767] :AFTER[SQUAD] pass
[LOG 05:59:12.767] :BEFORE[SQUADEXPANSION] pass
[LOG 05:59:12.767] :FOR[SQUADEXPANSION] pass
[LOG 05:59:12.767] :AFTER[SQUADEXPANSION] pass
[LOG 05:59:12.769] :FINAL pass
[LOG 05:59:12.769] Done patching
```
And on ConfigCache, you must find what follows:

```
UrlConfig
{
	parentUrl = ModA/config.cfg
	PART
	{
		name = ModA-Part
		title = This is the title of ModA. And ModC was here!
	}
}
```

Remove `GameData/ModB` for the second test.

The sequence without `ModB` installed should be, obviously, different, and the `ModA-Part` should have the `tittle` untouched.


## Conclusion

As we can easily verify, there're no real dependencies between `:FOR` and `:NEEDS`. All we have is a clever sequence of well defined actions taken on well defined phases of the patching process.

This technique can prevent the Race to the Bottom, currently on progress, to the `:LAST` phase - as well the not so straightforward solution of using `zzzSomething` on the GameData, used as an attempt to avoid the Race to the `:FINAL` but that ended up just creating a new Race to the Bottom on the `zzzz`, and later to the `zzzzz` and so goes on when triangular dependencies need to be handled.

**One size does not fits all**: it's an exercise in futility to try to find a single, "simple", magical solution for every complex problem in existence.


## References

* Forum
	+ On [Unkerballed Start](https://forum.kerbalspaceprogram.com/index.php?/topic/196589-1101-unkerballed-start-v120-under-new-management-aug-28-2020/&do=findComment&comment=3848745), and addionally a confirmation [here](https://forum.kerbalspaceprogram.com/index.php?/topic/196589-1101-unkerballed-start-v120-under-new-management-aug-28-2020/&do=findComment&comment=3849076). 
