# HoloLens 2 Spatial Mesh Sharing

A HoloLens 2 application that extracts the device's spatial awareness mesh — the real-time 3D reconstruction of the surrounding environment — and shares it over a network to other clients via Photon PUN2.

Built as an independent lab project (Önálló laboratórium) at BME TMIT, 2021/22 semester 2..

---

## What It Does

HoloLens 2 continuously scans the physical environment using its depth sensors and builds an internal spatial mesh — a polygon representation of walls, floors, furniture, and other surfaces. This mesh is normally only accessible locally on the device.

This application:
1. Accesses the spatial mesh via **MixedRealityToolkit (MRTK) Spatial Awareness**
2. Extracts the mesh data and stores it in a `Dictionary` keyed by observer ID
3. Serializes the mesh and transmits it to a **Photon PUN2** server
4. Other connected clients receive and reconstruct the mesh

This enables shared spatial understanding — all connected devices can see the same reconstructed environment, not just their own local scan.

---

## Architecture

```
[HoloLens 2]
    |
    MRTK Spatial Awareness Observer
    → mesh data extracted to Dictionary
    → serialized
    |
    Photon PUN2 (cloud relay)
    |
[Other clients]
    → reconstruct mesh locally
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Device | HoloLens 2 |
| Engine | Unity |
| AR/MR framework | MixedRealityToolkit (MRTK) |
| Spatial sensing | MRTK Spatial Awareness system |
| Networking | Photon PUN2 |
| Language | C# |

---

## Key Implementation Details

**Mesh extraction:**  
MRTK's Spatial Awareness system provides observer objects that represent scanned surfaces. The app subscribes to observer events and stores incoming mesh data in a `Dictionary<int, MeshData>`, where the key is the observer ID. This allows updates to existing mesh regions without duplicating data.

**Networking:**  
Photon PUN2 handles the relay. Mesh data is serialized before transmission. The server acts as a passive relay — no server-side processing of the spatial data.

**Why Dictionary storage?**  
HoloLens continuously refines its spatial mesh as it learns more about the environment. Using a dictionary keyed by observer ID means that when a mesh region is updated, the app replaces the existing entry rather than appending a new one — keeping the shared mesh consistent.

---

## Relationship to Other Projects

This project is a precursor to the coordinate synchronization and surface sharing work in the [BSc thesis project](../AR-Cooperative-Map-Creation). That project solves a similar problem on mobile phones (ARFoundation + ARCore), without relying on a device with built-in depth sensing like HoloLens 2.

The HoloLens approach benefits from much higher-quality spatial data (dedicated depth sensors vs. phone camera-based plane detection), but is constrained to a single expensive device type.

---

