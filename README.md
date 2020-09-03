# Module Manager `:FOR[]:NEEDS[]` Proof of Concept

This is a Proof of Concept for using `:FOR[]:NEEDS[]` while patching third-parties add'ons for features introduced by one Add'On (and that not rarely depends on a third one).

## Introduction

WiP

## The Experiment

Three minimalistic *"mods"* where create to demonstrate the mechanism:

* `ModA`, simulating the target add'on in need to be patched
* `ModB`, simulating the soft dependency for `ModA`, but hard dependency for `ModC`
* `ModC`, the add'on that wants to patch `ModA` if and only if `ModB` is present.

The experiment consists on two test runs:

* **With** `ModB` present;
* **Without** `ModB` present.

And the experiment was run on two different KSP versions:

* KSP 1.3.1, using [KSPe](https://github.com/net-lisias-ksp/KSPAPIExtensions) and [MM /L Experimental](https://github.com/net-lisias-ksp/ModuleManager).
	+ As side effect, demonstrates that MM can be easily back ported down to 1.3.1 when careful coding is applied. 
		- And only **one** Unity call prevents it to be back ported to 1.2.2. For while. :) 
* KSP 1.10.1, using **only** ["Official" MM](https://forum.kerbalspaceprogram.com/index.php?/topic/50533-*).

Further testing on different KSP versions was considered unneeded.

### Evidences

The evidences for the test runs on my rig can be found below:

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
	+ On [Unkerballed Start](https://forum.kerbalspaceprogram.com/index.php?/topic/196589-1101-unkerballed-start-v120-under-new-management-aug-28-2020/&do=findComment&comment=3848345) 
