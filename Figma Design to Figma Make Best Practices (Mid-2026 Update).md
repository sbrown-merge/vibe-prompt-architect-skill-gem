# Using Figma Design with Figma Make Best Practices

---

Version 2.0.0

| These guidelines are equally applicable to various AI-powered, "vibe-centric" development platforms, including Google Stitch, Lovable, Claude Code, and similar tools. |
| :---- |

---

To get the most out of Figma Make’s AI generation, we have to move past "making it look good" and start "making it make sense." We are essentially providing the AI with a coded blueprint rather than a flat image.

Here is the comprehensive list of Dos and Don'ts for creating attached frames that translate your design intent with surgical precision, updated with the latest community insights from mid-2026.

## 1\. Structural Integrity (Auto Layout & Composition)

If your frames don't have Auto Layout, the AI is just guessing. Auto Layout provides the spatial logic that the AI needs to understand how elements relate to one another.

* Do: Use Nested Auto Layout for everything. Use stacks to define clear vertical and horizontal relationships. If you can’t resize it manually without it breaking, the AI won't be able to generate content for it reliably.

* Do: Build with Higher-Order Blocks. Don't just feed the AI atomic elements (isolated buttons or inputs). AI tools work best when they reference complete, higher-order compositions (e.g., full Cards, Headers, Feature Rows) that already encode your specific spacing, color, and layout logic.

* Do: Set Min/Max Dimensions. This is the "safety rail" for AI. It prevents Figma Make from generating a button that’s 4000px wide or a card that’s 10px tall.

* Do: Use "Fill Container" and "Hug Contents." This tells the AI which elements are flexible and which are dictated by their internal content.

* Don't: Use Groups (Cmd \+ G). Groups are invisible to layout logic. Always replace them with Auto Layout frames (Shift \+ A).

* Don't: Over-rely on Absolute Positioning. Use it only for UI flourishes (like notification badges). If the core layout relies on it, the AI will struggle to "flow" content into the frame.

## 2\. Dynamic Injections (Native Slots)

The native Slot Property is the bridge between a static component and a dynamic AI-ready template.

* **Do: Use the Native Slot Property (Cmd \+ Shift \+ S).** Convert frames within your components into slots to define explicit "injection zones" for the AI.

* **Do: Curate "Preferred Instances."** In your Slot settings, limit the AI's choices to specific components from your library. This ensures the AI "plays with the right Legos."

* **Do: Provide High-Quality Default Content.** Don't leave slots empty in your main component. The AI uses the default content as a reference for the style, density, and scale it should mimic.

* **Don't: Use Legacy "Placeholder" Components.** Swapping in an "Empty" component is now considered "dirty data." It adds unnecessary layers for the AI to parse.

* **Don't: Nest Slots too deeply.** Try to keep your hierarchy to 2-3 levels of slots maximum. Beyond that, the AI’s spatial reasoning can start to hallucinate.

## 3\. The Data Layer (Variables, Modes & Libraries)

Variables are the "logic" of your design. They allow Figma Make to understand theme, intent, and scale.

* **Do: Publish Your Library Frequently.** Figma Make relies heavily on your *published* design system to use your specific components instead of generic UI defaults. If you tweak a variable or update a master component, publish the library immediately before running a new Make prompt.

* **Do: Use Semantic Variables.** Name your variables by intent (e.g., Surface / Primary, Action / Hover) rather than values (e.g., Blue 500). The AI understands "Action" better than "Blue."

* **Do: Leverage Variable Modes.** If you have Light and Dark modes defined, Figma Make can contextually apply the correct theme to the generated output based on the environment you're working in.

* **Do: Use Extended Variables for Spacing.** Apply variable-based spacing and padding. This ensures the AI-generated frames maintain the "rhythm" of your design system.

## 4\. Design Hygiene, Naming & Code Sync

The AI reads your layer tree like a developer reads code. Clean layers equal clean output.

* **Do: Sync Naming with your Codebase (MCP Ready).** Name layers exactly how they appear in your engineering component library (e.g., FormItem/Input/Select). This context is critical for Figma Make and external Model Context Protocol (MCP) servers to map your design directly to functional React/code components.

* **Do: Name Layers Semantically.** Use a BEM-like structure (e.g., Card / Header / Title). This acts as metadata that guides the AI’s generation logic.

* **Do: Use Component Instances.** Keep your attached frames as instances of your main components. This allows the AI to reference the underlying properties, variants, and documentation.

* **Don’t: Wrap individual text layers in Frames.** Avoid using frames for individual text layers as they generate unnecessary \<div\> tags in Make prototypes, hindering text selection and editing. Use text-specific frames only for unique padding/spacing.

* **Don't: Leave "Ghost Layers."** Delete hidden layers or layers with 0% opacity. Figma Make often "sees" this data, leading to weird spacing gaps or hallucinated elements.

* **Don't: Detach Components.** Once you detach, you lose the "source of truth." The AI loses the connection to your design system’s rules and constraints.

## 5\. Prompting & Performance Management

As we push Figma Make harder, managing the tool itself becomes just as important as managing the design file.

* **Do: Prompt for File Splitting.** When generating web apps or complex code outputs, Figma Make tends to merge everything into a single, massive apps.tsx file. Explicitly instruct the AI in your prompt to "separate components into distinct modules or files" to keep the output manageable.

* **Don't: Hoard Version History.** Community reports show that files with highly complex iterative workflows (dozens or hundreds of Make versions) can suffer from slow performance and hit prompt context limits. If a file becomes sluggish or Make stops iterating, copy your frames to a *brand new file* to reset the version history back to 0\.

## Summary Cheat Sheet

| Feature | The "Do" (High Accuracy) | The "Don't" (AI Confusion) |
| :---- | :---- | :---- |
| **Layout** | Deeply nested Auto Layout & Higher-order Blocks | Groups, raw "Frames", or isolated atoms |
| **Sizing** | Fill, Hug, and Min/Max | Fixed widths/heights |
| **Dynamic Areas** | Native Slot Properties | Legacy Placeholder Components |
| **Slots** | Set "Preferred Instances" | Leave slots "unconstrained" |
| **Variables** | Semantic Naming & Published Libraries | Raw Hex codes or Local Styles |
| **Hygiene** | Sync layer names with Codebase components | Leave "Ghost" or 0% opacity layers |
| **Performance** | Copy to a new file to reset heavy version history | Let version history build up indefinitely |

| Designer's Perspective: When you attach a frame to Figma Make, ask yourself: *"If I gave this frame to a junior developer who couldn't speak my language, could they build it just by looking at the layer panel?"* If the answer is no, your Auto Layout or naming needs work. |
| :---- |

