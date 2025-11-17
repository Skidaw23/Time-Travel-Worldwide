## Three.js ES Module Migration

This project has been migrated from legacy UMD (global `THREE`) usage to modern ES Modules.

### Why?
- Recent Three.js versions (≥ r161) no longer ship UMD builds by default.
- ES modules enable tree-shaking, clearer dependency graphs, and future maintenance.
- Removes deprecation warnings and lowers risk of incompatibilities.

### Current Imports
```html
<script type="module">
  import * as THREE from 'https://cdn.jsdelivr.net/npm/three@0.168.0/build/three.module.js';
  import { OrbitControls } from 'https://cdn.jsdelivr.net/npm/three@0.168.0/examples/jsm/controls/OrbitControls.js';
  // ...
</script>
```

### Self-Hosting (Optional)
1. Download:
   - `build/three.module.js`
   - `examples/jsm/controls/OrbitControls.js`
2. Serve from your own origin (e.g. `/vendor/three/`).
3. Update import paths:
   ```js
   import * as THREE from '/vendor/three/three.module.js';
   import { OrbitControls } from '/vendor/three/controls/OrbitControls.js';
   ```

### Adding More Loaders / Controls
Use the `examples/jsm` path similarly:
```js
import { GLTFLoader } from 'https://cdn.jsdelivr.net/npm/three@0.168.0/examples/jsm/loaders/GLTFLoader.js';
```

### Version Pinning
The version is pinned to `0.168.0`. Upgrade by changing the CDN version number in import URLs and testing:
- Check release notes: https://github.com/mrdoob/three.js/releases
- Validate no breaking API changes for used classes (Scene, PerspectiveCamera, MeshPhongMaterial, OrbitControls, etc.)

### Accessibility
- Canvas is marked as decorative (`aria-hidden="true"`) with a surrounding container `aria-label`.
- City cards use semantic labels for time, temperature, date.

### Security
- No secrets or API keys.
- Static data only.
- Minimal external requests (one globe texture). To be fully offline, replace texture URL with a locally hosted image.

### Fallback / Degradation
- If CDN fails, page still loads static HTML; you can add a `<noscript>` or a module load error handler for user messaging later.

### Future Enhancements
- Convert to TypeScript for stricter typing.
- Add unit tests (Jest) for UI logic (e.g., suggestion filtering).
- Bundle with Vite/Webpack and self-host modules.
- Add CI workflow for lint + format + deployment (GitHub Pages / Cloudflare Pages).