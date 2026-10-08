# Graph format converter

A single, dependency-free HTML file that converts between graph file formats, lets you reshape the graph, and previews it with a live ForceAtlas2-style layout. Everything runs in your browser; no data is uploaded anywhere.

By [Johan Myrberger](https://www.linkedin.com/in/myrberger/)

## Use it

Open `graph-converter.html` in a browser, or host it anywhere static (for example GitHub Pages). Drop a file in, choose an output format, download.

The default case is GraphML in, two Kumu-style CSV files out.

## Formats

| Format | Input | Output |
|---|---|---|
| GraphML | yes | yes |
| GEXF 1.3 | yes | yes |
| GML | yes | yes |
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

## Transform

Replace all nodes of a chosen type with edges between their neighbours:

1. Pick the node attribute that holds the type, then one or more values.
2. Each removed node is bypassed: every incoming neighbour is linked to every outgoing neighbour (undirected neighbours are linked pairwise).
3. New edges get a `relation` attribute with the removed type, and optionally `via` with the removed node's label. In Kumu output, `relation` becomes the connection `Type`.

## Load from a URL

```
graph-converter.html?file=https://example.com/graph.graphml
graph-converter.html?file=…/nodes.csv&file=…/edges.csv&format=graphml
```

| Parameter | Meaning |
|---|---|
| `file` | URL of a file to load. Repeat for a nodes + edges CSV pair. |
| `format` | Preselected output: `csv`, `graphml`, `gexf`, `gml`, `dot`, `json`, `cyjs`, `list` |
| `style` | CSV style: `kumu` or `gephi` |

The server hosting the file must allow cross-origin requests (CORS). Browsers usually block local files when the page is opened from disk.

## Limitations

- Visual data (colors, sizes, positions), nested graphs and hyperedges are not preserved.
- GML output keeps direction per graph and uses integer node ids.
- Edge-list output carries no direction, labels or attributes.
- The preview layout slows down above roughly 1,500 nodes.

## License

Add a license of your choice (for example MIT) as `LICENSE`.
