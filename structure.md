# pnext-core

* "Manager" object (`new PNext()`?):
  * Keeps the minimal state and config necessary for loading/unloading octree nodes (pointBudget, minNodeSize).
  * Can update one or more `PointCloudOctree`s and trigger the loading of needed nodes.
  * Can perform queries into one or more octrees, delegating down to `PointCloudOctree` as much as possible:
    * Get all the points along a line (profile)
    * Get volume above/below a polygon
    * Get number of points or all the points within a certain distance of a point.
    * etc.
* Data loaders:
  * POC
  * Las/Laz
  * Greyhound
  * Web-workers
  * others?
* Octree-related objects which wrap the geometry loaded by the loaders (above) and can be added to a Three.js `Scene`:
  * `PointCloudOctree` - Three.js `Object3D` which can be added to a Scene.
  * `PointCloudOctreeNode`
  * `PointCloudOctreeGeometry` - Built by the loaders to contain the geometry for a node.
  * `PointCloudOctreeGeometryNode`
* Materials
  * `PointCloudMaterial`
  * `EDLMaterial`
  * Shaders
* Default classification array and interfaces.
* Default gradient array and interfaces.

# pnext-utils
Three.js objects which are not necessary for the core task of displaying a point cloud but which can be useful for other packages or applications:
* `Profile` - Displays the actual profile in the Three.js `Scene`
* Debug helpers
* Measurement 3D objects:
  * Line
  * Polygon
  * Point
* Text rendering utilities.

# pnext-state
Defines the models necessary to represent the state of the full pnext application:
  * `PointCloud`
  * `Material`
  * `Feature`: (following geojson here)
    * `Line`
    * `Polygon`
    * `Point`
  * etc
* Should not know about Three.js at all.
* All models should be serializable and deserializable.

# pnext-react / pnext-angular / pnext-vue
Provides components which can be used to easily use pnext inside of a react application:
```
<Viewer pointBudget={1000000} minNodeSize={150}>
  <PointCloud material={material}>
    <Line coords={}>
    <Polygon coords={}>
    <Point coord={}>
    <Profile />
  </PointCloud>
</Viewer>
```
* The viewer component would display a simple Three.js viewer, without any UI.
* Applications could use the provided viewer or integrate into their own.
* Similar to https://github.com/Izzimach/react-three ?

# pnext-viewer
* Provides an actual UI that lets users interact with the 3D scene.
* Gets distributed as an app (electron?) like Potree.
* React-based, making use of `pnext-react`?

# Possible Architecture

```
                             +-------------+
                             |             |
                         +---+  three.js   <--+
                         |   |             |  |
                         |   +-------------+  |
                         |                    |
                         |                    |
                         |   +-------------+  |
                         |   |             +--+
                         <---+ pnext-core  |
                         |   |             <-+
                         |   +-------------+ |
                         |                   | Components update properties
                         |                   | of core/threejs objects and
                         |   +-------------+ | triggers re-rendering
                         |   |             +-+
Events from the canvas   <---+ pnext-react |
or loading trigger state |   |             <-+
updates                  |   +-------------+ |
                         |                   | State changes trigger
                         |                   | react updates/re-render
                         |   +-------------+ |
                         |   |             +-+
                         +---> pnext-state |
                           +-+             <--+
                           | +-------------+  |
                           |                  | User interaction
          State changes    |                  | updates state
          update viewer UI | +--------------+ |
                           | |              | |
                           +-> pnext viewer +-+
                             |              |
                             +--------------+

```
