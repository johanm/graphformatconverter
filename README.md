# Graph format converter

A single, dependency-free HTML file that converts between graph file formats, lets you reshape the graph, and previews it with a live ForceAtlas2-style layout. Everything runs in your browser; no data is uploaded anywhere.

By [Johan Myrberger](https://www.linkedin.com/in/myrberger/)

## Use it

**Run it directly:** <https://johanm.github.io/graphformatconverter/graphformatconverter.html>

Or download `graphformatconverter.html` and open it locally, or host it anywhere static. Drop a file in, choose an output format, download.

The default case is GraphML in, two Kumu-style CSV files out.

## Formats

| Format | Input | Output |
|---|---|---|
| GraphML | yes | yes |
| GEXF 1.3 | yes | yes |
| GML | yes | yes |
| Pajek `.net` | yes | yes |
| Graphviz DOT | yes | yes |
| JSON node-link (D3, NetworkX) | yes | yes |
| Cytoscape.js JSON | yes | yes |
| CSV, nodes + edges | yes | yes (Kumu or Gephi columns) |
| Plain edge list (`a b [weight]`) | yes | yes |

For CSV input, drop the nodes file and the edges file together. A file with `Source`/`From` and `Target`/`To` columns is taken as the edge list.

### CSV column styles

| | Nodes | Edges |
|---|---|---|
| **Kumu** | `Label`, `Type`, `Description`, `Tags`, … | `From`, `To`, `Type`, `Direction`, `Description`, `Tags`, … |
| **Gephi** | `Id`, `Label`, … | `Source`, `Target`, `Type`, `Id`, `Label`, `Weight`, … |

Kumu links elements by label, so `From`/`To` hold labels. Duplicate labels are made unique, with a warning. Other attributes become extra columns.

Gephi reserves `Type` for Directed/Undirected, so a connection type (for example from Kumu or the transform) is written as a `relation` column in Gephi output.

## Transform

Replace all nodes of a chosen type with edges between their neighbours:

1. Pick the node attribute that holds the type, then one or more values.
2. Choose how neighbours are connected:
   - **Connect all neighbours** (default): every pair of neighbours of a removed node is linked by an undirected edge, whatever the original directions. Removing the posts from a `user -> post` "liked" graph links users who liked the same post.
   - **Follow edge direction**: an incoming neighbour is linked to an outgoing one (A -> X -> B becomes A -> B). A node with only incoming or only outgoing edges creates no new edges in this mode.
3. New edges get a `relation` attribute with the removed type, and optionally `via` with the removed node's label. In Kumu output, `relation` becomes the connection `Type`.

### Remove individual nodes

Click a node in the graph preview and choose **Remove node**. The node and its edges are deleted from the graph and from the exported files. Removed nodes are listed in the Transform section, where each one can be restored (with its edges), or all at once. Manual removals are applied first, then the type transform above. Loading a new file clears the list.

## Load from a URL

```
https://johanm.github.io/graphformatconverter/graphformatconverter.html?file=https://example.com/graph.graphml
https://johanm.github.io/graphformatconverter/graphformatconverter.html?file=…/nodes.csv&file=…/edges.csv&format=graphml
```

| Parameter | Meaning |
|---|---|
| `file` | URL of a file to load. Repeat for a nodes + edges CSV pair. |
| `format` | Preselected output: `csv`, `graphml`, `gexf`, `gml`, `net`, `dot`, `json`, `cyjs`, `list` |
| `style` | CSV style: `kumu` or `gephi` |

The server hosting the file must allow cross-origin requests (CORS). Browsers usually block local files when the page is opened from disk.

## Limitations

- Visual data (colors, sizes, positions), nested graphs and hyperedges are not preserved.
- GML output keeps direction per graph and uses integer node ids.
- Pajek output keeps labels, direction and weight only.
- Edge-list output carries no direction, labels or attributes.
- The preview layout slows down above roughly 1,500 nodes.

## Examples

Two small GraphML files in this repo, each with two node types (`Person` and `Project`) so the transform has something to chew on. Open one, tick **Replace nodes of one type with edges**, choose the attribute `type`, and try removing each type in turn.

**[`example-people-projects.graphml`](https://johanm.github.io/graphformatconverter/graphformatconverter.html?file=example-people-projects.graphml)** is a true bipartite graph: people are linked only to projects.
- Remove `Project`: a collaboration network, where people who share a project become linked.
- Remove `Person`: projects become linked through shared people. Bob and Carol each link Apollo and Borealis, giving two parallel edges that the `via` attribute tells apart.

**[`example-people-projects-mentoring.graphml`](https://johanm.github.io/graphformatconverter/graphformatconverter.html?file=example-people-projects-mentoring.graphml)** is the same graph plus one person-to-person edge (Alice mentors Eve), so it is not bipartite.
- Remove `Project`: the same collaboration network, with the mentoring edge kept as-is.
- Remove `Person`: the mentoring link chains through Alice and Eve and also links Apollo to Cedar, so the three projects form a triangle.

