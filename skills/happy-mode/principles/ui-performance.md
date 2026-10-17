# UI performance

Keep UI execution responsive at almost any cost, in every language and framework. This applies equally to mobile, web, desktop, native, and cross-platform UI. Give main/UI, input, and render-critical threads or serialized executors the least possible workload. Only minimal framework-required UI work belongs on its required thread; convenience is not a reason to run other work synchronously.

## Plan for 120 Hz or faster

Assume 120 Hz or faster as the engineering planning baseline, regardless of whether every physical device supports it. A whole frame lasts about 16.67 ms at 60 Hz, 8.33 ms at 120 Hz, and 6.94 ms at 144 Hz. Plan around at most roughly 8 ms for the entire frame, with a tighter deadline at higher actual refresh rates.

This is a shared frame budget, never a per-task allowance. Input, framework work, layout, render submission, and other queued work all need time. A single 8 ms application task already consumes essentially the entire 120 Hz frame and leaves no headroom. Keep synchronous application work substantially below the frame budget; never deliberately block for up to 8 ms. Offload heavy work or split it into bounded units where appropriate, without flooding the UI executor with continuations.

## Keep work off the UI thread

- Use asynchronous I/O and appropriate background executors, workers, or isolates for CPU work, parsing, decoding, transformations, and data processing. Deliver a prepared result back for the smallest required UI update. In cross-platform runtimes, protect the JavaScript or other UI-critical execution thread too, even when it is separate from the native main thread.
- Keep UI-affine operations on the thread or executor the framework requires. Never mutate thread-confined views or UI state from a worker. Keep required synchronous rendering, state commits, and input handling small; move preparatory work out of them.
- Never block rendering, animation, or input handlers on locks, sleeps, synchronous I/O, or another task's completion. `async`/`await`, promises, microtasks, timers, frame callbacks, and tasks launched on a UI executor do not by themselves move CPU work to the background. Bound background concurrency and prioritize visible interaction so workers do not starve the UI through contention or floods of completion callbacks.

**Platform examples, not scope limits:**

- Android: explicitly move heavy work to workload-appropriate dispatchers or executors, such as `Dispatchers.IO` for blocking I/O and a bounded compute executor or `Dispatchers.Default` for CPU work, following repository conventions. Keep view updates on Main. No Main-thread `runBlocking`, `Future.get()`, `Thread.sleep`, or blocking lock acquisition.
- iOS: keep only required UIKit or SwiftUI updates on the main thread/`MainActor`. Move heavy work to an appropriate background executor or dispatch queue with explicit isolation. A Swift `Task` created on `MainActor` can inherit that actor; `async`/`await` is not a background CPU guarantee. Never mutate UIKit from a worker or block main on semaphores, synchronous dispatch waits, or I/O.
- Web: use workers for heavy CPU and data processing. DOM access and layout measurements stay on the main thread but must be minimized and cached. Scheduling main-thread code with promises, microtasks, timers, or `requestAnimationFrame` leaves that work on the same thread; these are not worker dispatch mechanisms.

**Antipatterns in any stack:** synchronous network or disk access, large parsing or image decoding on a UI-critical thread; moving heavy work into a UI-thread task or timer and calling it background work; dispatching computation elsewhere and immediately blocking UI execution for the result.

## Native iOS architecture

For new native iOS UI, avoid SwiftUI and prefer UIKit. This is the engineering default for this workflow: UIKit gives explicit control over view lifecycle, layout, invalidation, and scheduling, making it easier to keep the critical UI path small. Framework choice alone does not prove responsiveness; profile the actual implementation.

For rich or heavy surfaces, prefer a suitable asynchronous layout/rendering library such as Texture (AsyncDisplayKit) when it fits the repository and task. Texture provides documented thread-safe node and measurement APIs that can move preparation, text sizing, image decoding, layout, and rendering off main. Evaluate integration and maintenance costs before adding a dependency. See its [getting-started guide](https://texturegroup.org/docs/getting-started.html) and [threading model](https://texturegroup.org/development/threading.html).

Follow each API's thread contract. Ordinary UIKit access, view layout mutations, and integration stay on main; access Texture nodes' `.view` and `.layer` only on main too. Use only documented thread-safe node/measurement APIs off main. Texture does not make arbitrary UIKit operations safe on a worker.

Respect explicit task requirements and repository constraints. Do not turn a scoped change into an app-wide SwiftUI migration. When existing SwiftUI must remain, isolate and minimize `MainActor` work, avoid expanding SwiftUI by default, and prefer a UIKit boundary for new performance-critical surfaces where the repository permits it.

## Run animations independently of the UI thread

Always move animation timing and interpolation off the main/UI/JavaScript thread using native animation, compositor, render-thread, or GPU facilities that run independently of application callbacks wherever the platform supports them. Main-thread blocking must not pause an animation that can run independently. Choose APIs that actually continue while the application thread is busy; a GPU-friendly transform alone does not establish that independence.

Hand the timeline, keyframes, and interpolation to an eligible independent rendering path. Moving tween calculations into a worker but posting every visual update back through main/UI/JavaScript still makes progress depend on that busy thread. Check the actual API, animated properties, and target platform: a native API, CSS animation, or Web Animations API call is not automatically eligible for independent compositing.

Do not use main-thread timers, `requestAnimationFrame`, or per-frame application callbacks as the animation engine. Keep framework-required setup and commits on their owning thread, minimal and bounded. If the platform requires synchronous UI work that cannot be delegated, document why, minimize it, and verify its cost against the actual refresh-rate deadline. Preserve UIKit, DOM, and other framework thread contracts; this rule never authorizes arbitrary view access from a worker.

Use scale, translate, and rotate transforms as the default across platforms, languages, and frameworks. Use compositor opacity for a fade when appropriate. Prepare geometry and visual content before motion starts, then let the independent animation facility interpolate prepared state.

Independent animation still shares compositor, render-thread, and GPU capacity; it cannot guarantee that frames never stall. Verify animation scheduling and those workloads in a target-platform trace on actual hardware, including whether main/UI/JavaScript thread activity pauses motion.

Do not animate sizes, layout offsets, constraints, or repaint-heavy properties when a transform expresses the same interaction. Transforms are the default, not proof of GPU acceleration: clipping, filters, rasterization, oversized layers, and blending can still make them expensive. Verify the actual rendering path; do not promote every element to a layer or add a framework to claim acceleration.

**Antipatterns:** CSS `top`, `left`, `width`, or `height` animation; repeatedly changing native layout parameters or constraints to move a view; animating shadows, blur, or other paint-heavy effects instead of transforming prepared content.

## Cache geometry and invalidate deliberately

Precompute and cache positions, offsets, and geometry. Recompute when layout, content, container size, or another real dependency changes. Never poll offsets or recalculate layout on every animation frame merely because a frame callback ran.

Direct user interaction such as dragging or a scroll gesture can require a cheap update each frame. Prefer platform-driven interaction facilities; where UI-affine commits are necessary, coalesce input to one minimal update per display frame, use the latest input and cached geometry, and apply transform deltas. A frame callback is only a coalescing mechanism for these bounded commits, never the default animation engine. Measure only when the interaction or an actual invalidation requires it. Batch necessary reads before writes; never alternate them in a loop and force repeated synchronous layout.

**Antipatterns:** a perpetual `requestAnimationFrame` loop calling `getBoundingClientRect()` or layout-dependent `getComputedStyle()` and writing styles each frame; remeasuring every child on every pointer event; recomputing unchanged native offsets in each frame callback. A frame callback that applies a cheap transform from cached state is not the problem.

## Prove responsiveness

For changes affecting UI work, animation, or layout, capture runtime profiler or frame-trace evidence on the actual surface and representative hardware. In every target environment, include realistic data and input during loading, dragging, scrolling, and animation. Check critical-thread task duration and headroom against the actual refresh-rate budget, input response, frame deadlines, layout/paint activity, worker contention, and render-thread/GPU load. Verify that independently scheduled animation continues during application-thread activity. Compare before and after where behavior changes. A screenshot, passing type check, or transform declaration does not prove the absence of blocking or jank. Report unmeasured paths and tooling limits explicitly.

Verify at the target refresh rate of 120 Hz or higher, or state hardware and verification limits. Inspect missed frame deadlines and tail/worst-frame times under input and background load, not average FPS alone. Leave substantial headroom for other runtime, framework, input, layout, and render work; several individually small tasks can consume the same frame together.
