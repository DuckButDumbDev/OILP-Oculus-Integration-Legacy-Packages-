# OILP: Oculus Integration Legacy Packages (Ported for Unity 2020–2022)
By DuckButDumbVR

**OILP** provides patched, fixed versions of the classic legacy **Oculus Integration SDK** frameworks (originally built in the 2018–2020 era). It addresses the extensive codebase deprecation cascading errors that happen when importing older asset utilities into modern Unity editors.

This project strips out dead legacy wrapper dependencies (like `UnityEngine.Experimental.XR.Boundary`, `PlayerSettings.VROculus`, and `XRDevice.isPresent`) and optimizes compilation pathways to allow backward-compatible development on mobile standalone architectures.

---

## 💻 Recommended Environments

For optimal stability and deployment performance, use the following editor targets:
*   **Unity 2020.3 LTS** (Specifically tested up to `2020.3.48f1`)
*   **Unity 2021.3 LTS** (Specifically tested up to `2021.3.24f1`)
*   **Unity 2019.4 LTS** (Legacy baseline support)

---

## 🛠️ Main Patches Applied

### 1. Modern Boundary Routing (`OVRBoundary.cs`)
*   **The Issue:** Unity 2020.2+ completely dropped `UnityEngine.Experimental.XR.Boundary`.
*   **The Fix:** Removed broken generic Unity engine fallbacks. Bypassed the deprecated alias mapping tree to explicitly interface with pure, low-level platform calls via `OVRPlugin`. 

### 2. Modern HTTP Upgrades (`UnityWebRequest`)
*   **The Issue:** Older syntax checking protocols like `webRequest.result.ProtocolError` or old boolean flags cause fatal compiler errors in newer engine frameworks.
*   **The Fix:** Migrated web calls to modern `UnityEngine.Networking.UnityWebRequest.Result` enum paths.

### 3. Cleaning Obsolete Lint Configurations (`OVRLint.cs` / `EnforceVRSupport`)
*   **The Issue:** Legacy PC-VR layout routines (such as Oculus Rift Dash integration and `PlayerSettings.virtualRealitySupported`) trigger context errors due to architecture changes.
*   **The Fix:** Truncated and commented out outdated Editor tasks since XR execution flags are now managed directly by the user interface inside modern project pipelines.

---

## 📦 Installation & Setup

1. **Clean Workspace:** Ensure any existing copy of the official Meta XR / Oculus SDK is completely removed from your project directory before cloning.
2. **Download Package:** Download the matching package zip file from our Releases tab, or clone this repository straight into your workspace.
3. **Configure Project Settings:**
   * Go to **Edit > Project Settings > XR Plug-in Management**.
   * Under the **Android Platform** tab (the green robot icon), check the **Oculus** activation box to activate support manually.
4. **Regenerate Assembly Files (If compilation error alerts persist):**
   * Go to **Edit > Project Settings > External Tools**.
   * Check all options under *Generate .csproj files for:* (Embedded packages, Local packages, etc.).
   * Click **Regenerate project files**.

---

## 💡 Code Syntax Overview (Safe 3-DOF vs 6-DOF Management)

When writing cross-platform routines targeting both 3-DOF headsets (Oculus Go) and 6-DOF devices (Oculus Quest) within this repository, use the following defensive runtime model to handle boundary system queries safely:

```csharp
using UnityEngine;

public class SecurityBoundsManager : MonoBehaviour
{
    void Start()
    {
        // Safe runtime validation context check
        if (OVRManager.boundary != null && OVRManager.boundary.GetConfigured())
        {
            // Executed exclusively on Quest: 6-DOF tracking available
            Vector3 spaceDimensions = OVRManager.boundary.GetDimensions(OVRBoundary.BoundaryType.PlayArea);
            bool isActive = OVRManager.boundary.GetVisible();
        }
        else
        {
            // Executed on Oculus Go: Fallback tracking logic implemented here
            Debug.Log("Device lacks a physical Guardian Boundary System (Oculus Go Mode active).");
        }
    }
}



//Please refer to the source header blocks inside the repository folders for specific notices.
