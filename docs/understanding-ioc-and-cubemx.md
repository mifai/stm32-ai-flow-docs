# Understanding `.ioc`, CubeMX, and Workflow Changes

This guide explains the role of the `.ioc` file, the role of STM32CubeMX, and
how this workflow is allowed to work with `.ioc` safely.

It is written for readers who want the practical mental model first, not a
list of commands.

## The Short Version

If a project uses STM32CubeMX, the `.ioc` file is the source of truth for
generated hardware and middleware configuration.

CubeMX is the tool that understands that configuration natively and can turn
it into generated project files.

The workflow can work with `.ioc`, but it should treat `.ioc` with respect:

- CubeMX remains the authoritative editor for complex configuration work
- the workflow may apply controlled, repeatable `.ioc` changes when they are
  well understood
- generated configuration should not drift away from `.ioc`
- if `.ioc` cannot be updated reliably, the workflow should not pretend the
  problem was solved by hand-editing generated configuration elsewhere

That is the core idea.

The relationship is easier to grasp visually:

![How `.ioc`, CubeMX, the workflow, generated files, and project logic fit together](assets/ioc-cubemx-workflow-preview.svg)

## What The `.ioc` File Really Is

The `.ioc` file is not just a settings file in the casual sense.

In an STM32 project, it is the project description that tells CubeMX things
such as:

- which MCU or board is being targeted
- which pins are assigned to which signals
- which peripherals are enabled
- how clocks are configured
- how DMA, interrupts, middleware, and virtual pins are described
- how code generation should interpret the project structure

When people say ".ioc is the source of truth," they mean that generated
hardware and middleware setup is supposed to be derived from this file, not
invented separately in source code.

That does not mean every important behavior in the project belongs in `.ioc`.
It means configuration that CubeMX owns should stay anchored there.

## What CubeMX Is Responsible For

CubeMX is the native configuration tool for the `.ioc` model.

Its strengths are:

- understanding the STM32 device model
- presenting clocks, pins, peripherals, middleware, and generated options as
  one coherent configuration space
- updating many related settings together instead of expecting a human to know
  every hidden dependency
- regenerating project files from that configuration

CubeMX is therefore the safest place to make new or complicated configuration
changes, especially when one visible setting implies several hidden changes
behind the scenes.

Typical examples include:

- enabling a peripheral for the first time
- changing clock-tree topology
- enabling DMA paths
- introducing middleware such as USB or FreeRTOS
- changing virtual-pin or generated-task metadata

In those cases, CubeMX is usually the right first move because it knows the
full configuration model better than a manual editor or a narrow script does.

## What The Workflow Is Responsible For

The workflow is not meant to replace CubeMX as a full interactive
configuration environment.

Its job is to keep the project disciplined around `.ioc`:

- preserving the rule that `.ioc` is authoritative for generated
  configuration
- applying repeatable `.ioc` changes when they are already understood well
- validating that `.ioc` is still internally coherent
- regenerating code from `.ioc`
- reconciling project-owned logic with generated files after regeneration
- reporting clearly when the result is valid, questionable, or blocked

The workflow is strongest when it turns repeated procedure into a safe,
reviewable routine.

## What "Modifying `.ioc` From Inside The Workflow" Means

Inside this workflow, modifying `.ioc` does not mean trying to imitate all of
CubeMX blindly.

It means making narrow, structured, reviewable changes to the file when those
changes are already well understood.

In practice, that usually means:

- applying a known reusable change pattern
- making a small set of coordinated key updates
- preserving the file as the source of truth rather than bypassing it
- validating the result immediately after the change

This is useful because many STM32 changes are repeated across projects or
across branches of the same project. Once such a change is understood, the
workflow can often apply it reliably without reopening the full CubeMX GUI for
every repetition.

## What The Workflow Can Safely Change

The workflow is a good fit for `.ioc` changes when the change is:

- repeatable
- already understood
- limited in scope
- easy to validate afterward
- expressible as a small number of coordinated edits

This usually includes:

- enabling a known project pattern that has already been used before
- applying a reusable configuration profile to another compatible STM32 target
- making small metadata adjustments that are already part of an established
  workflow pattern
- replaying a change that was first created and verified in CubeMX

The key idea is not "simple versus advanced" in the abstract.
The real distinction is "known and controlled" versus "new and uncertain."

## When CubeMX Should Be Preferred

CubeMX should usually be your first choice when the change is new, broad, or
not yet fully understood.

Use CubeMX first when:

- you are enabling a peripheral or middleware stack for the first time
- the change touches clocks, pins, DMA, interrupts, generated tasks, or
  several related configuration areas at once
- you are not fully sure which `.ioc` fields must stay in sync
- the change depends on CubeMX-specific metadata that would be risky to guess
- you want the visual confirmation that the project configuration still makes
  sense as a whole

This is especially important for changes that look small in the UI but expand
into many linked `.ioc` fields behind the scenes.

## When Workflow-Side `.ioc` Changes Make Sense

Workflow-side `.ioc` changes make sense when the project already knows the
shape of the change and wants to make it repeatable.

Good candidates include:

- replaying a known peripheral baseline on another board of the same family
- reapplying an already-proven middleware baseline
- carrying a known `.ioc` adjustment into a fresh branch after earlier work
  established the right pattern
- keeping repeated project variants aligned without hand-editing the file each
  time

The healthy pattern is often:

1. Make the change once in CubeMX.
2. Confirm that the generated result is correct.
3. Treat that result as a known pattern.
4. Let the workflow reuse that pattern later when needed.

That is very different from asking the workflow to guess a brand-new CubeMX
configuration from scratch.

## What The Workflow Must Not Do

If `.ioc` is the source of truth, then the workflow must not quietly "solve"
configuration changes by editing generated configuration outside the agreed
ownership boundary.

That means it must not:

- treat generated initialization code as the real authority when `.ioc` says
  otherwise
- hand-edit generated configuration as a substitute for updating `.ioc`
- claim success when `.ioc` and generated code are drifting apart
- continue with uncertain non-user-block configuration edits when reliable
  `.ioc` synchronization is blocked

When the correct `.ioc` change is unclear, the honest answer is not to fake
certainty. The honest answer is to stop and say that CubeMX or user direction
is needed.

## How Generated Files Fit Into This Model

Generated files are an output of the `.ioc` configuration, not a second source
of truth.

That has two important consequences:

First, project-specific behavior should live outside generated glue whenever
possible, or inside clearly protected user sections when it must live in a
generated file.

Second, when generated files are refreshed, the workflow should preserve the
agreed ownership boundary rather than encouraging manual merge chaos.

This is why the workflow distinguishes between:

- configuration authority in `.ioc`
- generation performed by CubeMX
- project-owned runtime behavior in modules and protected user logic

## How To Decide Which Tool To Use

When deciding between CubeMX and workflow-side `.ioc` changes, ask one
question:

"Is this a known, reviewable pattern, or am I still discovering the correct
configuration?"

If you are still discovering it, use CubeMX.

If the configuration is already known and the goal is to apply it safely and
repeatably, the workflow may be the better tool.

Another useful test is this:

- if getting one field wrong could leave hidden related fields inconsistent,
  prefer CubeMX
- if the change has already been proven and can be validated mechanically,
  workflow-side modification becomes reasonable

## How To Check That The Result Is Still Sound

A successful result is not just "the file changed" or "the build passed."

You want to know whether the `.ioc` change remained coherent as configuration.

The workflow therefore treats integrity checking as a separate concern.

After a meaningful `.ioc` change, you should confirm:

- the `.ioc` file is internally coherent
- related keys still agree with each other
- generated outputs can still be produced from the `.ioc`
- project-owned logic still survives regeneration correctly
- newly enabled peripherals do not leave the project missing required local
  driver support

It is also usually worth opening CubeMX and checking whether it shows
warnings, red markers, conflicts, or anything else that looks suspicious.

That is especially useful after workflow-side `.ioc` edits, because CubeMX is
still the native tool that understands the full configuration model. It does
not have to be repeated after every tiny, already-proven change, but it is a
very good sanity check after significant or uncertain modifications.

Not every warning means the configuration is actually wrong, but every warning
should be understood. If CubeMX shows something questionable, the healthy
response is to understand why it appears and either fix it or consciously
accept it for a known reason.

In human terms, you are checking two different kinds of health:

- configuration health: does `.ioc` still describe a sensible STM32 setup?
- regeneration health: can the project still move from `.ioc` to generated
  files and then to a working build without drift?

Both matter.

## What "Integrity" Means In Practice

For this workflow, integrity is not a vague feeling.

It means the project still respects the ownership model:

- `.ioc` matches the generated configuration intent
- generated files are aligned with the current `.ioc`
- project-owned logic remains in its proper place
- no hidden mismatch was introduced in clocks, DMA, interrupts, pins,
  middleware, or related metadata
- the result is honest about what has and has not been validated

This is why the workflow separates checking, regeneration, reconciliation, and
validation instead of collapsing everything into one vague success message.

## A Healthy Working Habit

The most reliable habit is:

1. Decide whether the change is new or already known.
2. Use CubeMX for new discovery work.
3. Use workflow-side `.ioc` changes for known repeatable patterns.
4. Regenerate and reconcile in the normal project path.
5. Check integrity before claiming success.

That habit keeps the source of truth clear and prevents configuration drift
from accumulating quietly over time.

## The One Mistake To Avoid

The most damaging mistake is to treat `.ioc` as theoretically authoritative
while making real configuration decisions somewhere else.

Once that happens, the project may still build for a while, but it becomes
harder to regenerate safely, harder to review, and harder to trust.

The workflow is designed to prevent exactly that failure mode.

## Final Mental Model

Think of the system like this:

- `.ioc` describes the CubeMX-owned configuration truth
- CubeMX is the native editor and generator for that truth
- the workflow helps preserve, reuse, validate, and operationalize that truth
- project logic belongs outside generated configuration whenever possible

If you remember that division of responsibility, most decisions about `.ioc`
become much easier.
