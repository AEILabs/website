

## Problem

The page renders blank. The most likely causes:

1. **Nested `<mesh>` inside `<mesh>`** (lines 102-106) — a wireframe mesh is nested as a child of the globe mesh, which is invalid in R3F. Children of `<mesh>` are expected to be geometry/material, not other meshes.

2. **`<bufferAttribute>` usage** — declarative `<bufferAttribute>` in R3F v8 can silently crash. The `FlowingParticles` and `Stars` components both use this pattern. The safer approach is to create `BufferGeometry` imperatively with `useMemo` and pass it as a prop.

3. **Canvas container height** — the parent `<div className="absolute inset-0">` should work, but adding explicit `w-full h-full` ensures the Canvas gets dimensions.

## Plan

### 1. Fix nested mesh in `GlobeCore`
Move the wireframe `<mesh>` to be a sibling of the globe mesh inside the `<group>`, not a child.

### 2. Replace declarative `<bufferAttribute>` with imperative geometry
In both `FlowingParticles` and `Stars`, create `THREE.BufferGeometry` in `useMemo` with `setAttribute`, then pass via `<points geometry={geo}>` instead of using JSX `<bufferAttribute>`.

### 3. Add explicit dimensions to Canvas container
Change the wrapper div to `className="absolute inset-0 w-full h-full"`.

### 4. Wrap Canvas in an error boundary (optional safety)
Add a simple React error boundary around the Canvas so Three.js errors don't blank the entire page.

