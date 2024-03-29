# Request JSON
The **Request JSON** is used for the **CalculateLayout** endpoint of the *Symbio-Graphic-Service*. Layout calculation can be requested for
* the ```calculation``` type ```cxn``` (connection; route for a single connection path from a source node to a target node) and
* the ```calculation``` type ```flow``` (process flow; directed graph with any number of nodes and edges).

From now on the description refers to the ```calculation``` type ```flow```.<br/>

## Table of contents
* [1.0 - General structure of the Request JSON](#gen-struct)
* [2.0 - The diagram meta-information part of the Request JSON](#dgm-meta)
  * [2.1 - How it typically looks like](#dgm-meta-typic)
  * [2.2 - The requested <code>result</code> type](#dgm-meta-result)
  * [2.3 - The requested <code>calculation</code> type](#dgm-meta-calc)
  * [2.4 - The <code>configurations</code> ](#dgm-meta-conf)
* [3.0 - The context part of the Request JSON (diagram as a whole)](#context-part)
  * [3.1 - How it typically looks like](#context-part-typic)
  * [3.2 - Context informations](#context-part-info)
* [4.0 - The <code>content</code> part of the Request JSON (containing the nodes and edges collections)](#content-part)
  * [4.1 - How it typically looks like](#content-part-typic)
  * [4.2 - The <code>nodes</code> collection](#content-part-nodes)
    * [The <code>laneRelevantRelated</code> collection](#content-part-nodes-lane-relevance)
  * [4.3 - The <code>edges</code> collection](#content-part-edges)
* [5.0 - The <code>pools</code> part of the Request JSON ](#pools-part)
  * [5.1 - How it typically looks like](#pools-part-typic)
  * [5.2 - The <code>pool</code> tuning](#pools-part-tune)
* [6.0 - The <code>elements</code> part of the Request JSON](#elements-part)
  * [6.1 - How it typically looks like for events](#elements-part-typic-events)
  * [6.2 - How it typically looks like for tasks](#elements-part-typic-tasks)
  * [6.3 - How it typically looks like for roles](#elements-part-typic-roles)
  * [6.4 - How it typically looks like for data](#elements-part-typic-data) --- REWORK STOPED HERE

* [7.0 Understanding the correlation of <code>nodes</code> / <code>edges</code>  and <code>elements</code> by examples](#correlation)
  * [Sample 1 (a node/shape and the underlaying element vs. the BPMN equivalent)](#correlation-sample1)

## <a name="gen-struct">1. - General structure of the Request JSON</a>
The **Request JSON** (for ```calculation``` type ```flow```) must provide all necessary information to calculate a layout (and optionally render a graphic, in the case the ```result``` type is ```esvg```, ```svg```, ```sesvg``` or ```ssvg```) of a process flow (directed graph with any number of nodes and edges).

**Disclaimer:** The following comparison between the *Request JSON* and *BPMN* is made not because the two formats perform a similar task, but because they convey similar content: **Process flows**.

<table>
<tr><th>The file structure</th><th>Compared to BPMN</th><th>Comments</th></tr>
<tr>
<td>
&#x25B6;

```
{
  "result": "json",
  "calculation": "flow",
  "configurations": [
    ...
  ],
```

&#x25B6;

```
  "context": {
    ...
  },
```

&#x25B6;

```
  "content": {
    "nodes": [
      {
        ...
      },
      ...
    ],
    "edges": [
      {
        ...
      },
      ...
    ]
  },

```

&#x25B6;

```
  "pools": [
    ...
  ],
```

&#x25B6;

```
  "elements": [
    ...
  ]
}
```

</td>
<td>
&#x25B6;

```
<definitions>





```

&#x25B6;
	
```
  <bpmndi:BPMNDiagram>
    ...
    ...
```

&#x25B6;

```
    <bpmndi:BPMNPlane>

      <bpmndi:BPMNShape>
      ...
      </bpmndi:BPMNShape>
      ...


      <bpmndi:BPMNEdge>
      ...
      </bpmndi:BPMNEdge>
      ...

    </bpmndi:BPMNPlane>
  </bpmndi:BPMNDiagram>
```

&#x25B6;

```
  <collaboration>
      ...
  </collaboration>
```

&#x25B6;

```
  <process>
      ...
  </process>
</definitions>
```

</td>
<td>
&#x25B6; <b><i>Diagram meta-information</i></b><br/>
The JSON format has to support layout and rendering requests for multiple purposes (<code>cxn</code> and <code>flow</code>) as well as multiple configurations.
<br/>
Thus it provides some meta-information like the requested <b><i>"result"</i></b> and the type of <b><i>"calculation"</i></b> as well as details regarding layout and rendering <b><i>"configurations"</i></b>.<br/><br/>
&#x25B6; <b><i>Diagram context</i></b><br/>
The JSON format holds additional information about the diagram, that are not known to BPMN.<br/><br/></b><br/>
&#x25B6; <b><i>Diagram content - Commonalities</i></b><br/>
Besides that, the Request JSON and BPMN hold directly comparable data:<br/>
- JSON <b><i>"nodes"</i></b> / <b>"<i>edges"</i></b> and the BPMN <b><i>&lt;bpmndi:BPMNPlain&gt;</i></b> contain the collection of node-shapes and edge-shapes.<br/>
- JSON <b><i>"pools"</i></b> and the BPMN <b><i>&lt;collaboration&gt;</i></b> contain the collection of pools.<br/>
- JSON <b><i>"elements"</i></b> and the BPMN <b><i>&lt;process&gt;</i></b> contain the collection of node-definitions and edge-definitions.<br/><br/>
<b><i>Diagram content - Differences</i></b><br/>
- While JSON provides diagram information as sub-nodes (e.g.: <i>"id": "1",</i>), BPMN provides diagram information as attributes (e.g.: <i>&lt;bpmn:process id="1"&gt;</i>).<br/>
- While JSON distinguishes the <b><i>"nodes"</i></b> collection and the <b>"<i>edges"</i></b> collection, BPMN doesn't.<br/><br/>
&#x25B6; <b><i>Pools</i></b><br/>
The Request JSON can hold and BPMN holds a collection of pools. While the Request JSON needs the collection of pools only to add an empty pool or to determine the order of the pools, BPMN always needs the collection of pools.<br/><br/>
&#x25B6; <b><i>Elements</i></b><br/>
The Request JSON and BPMN hold a collection elements. Each node referres to an element, multiple nodes can refer to the same element. The elements hold additional information, that can be shared across multiple nodes.<br/><br/>

</td>
</tr>
</table>

## <a name="dgm-meta">2.0 - The diagram meta-information part of the Request JSON</a>
### <a name="dgm-meta-typic">2.1 - How it typically looks like</a>
<table>
<tr><th>Example with request for one layout</th><th>Example with request for six layouts</th><th>Comments</th></tr>
<tr>
<td>

```
  "result": "json",
  "calculation": "flow",
  "configurations": [
    {
      "layoutType": "flow",
      "lcid": 1031,
      "vertical": true
    }
  ]
























.
```

</td>
<td>

```
  "result": "json",
  "calculation": "flow",
  "configurations": [
    {
      "layoutType": "flow",
      "lcid": 1031,
      "vertical": true
    },
    {
      "layoutType": "flow",
      "lcid": 1031,
      "vertical": false
    },
    {
      "layoutType": "swimlane-roles",
      "lcid": 1031,
      "vertical": true
    },
    {
      "layoutType": "swimlane-roles",
      "lcid": 1031,
      "vertical": false
    },
    {
      "layoutType": "swimlane-apps",
      "lcid": 1031,
      "vertical": true
    },
    {
      "layoutType": "swimlane-apps",
      "lcid": 1031,
      "vertical": false
    }
  ]
```

</td>
<td>

The the three available <b><i>"layoutType"</i></b>s
<ul>
<li> <b><i>"flow"</i></b> (layout without lanes or pools) </li>
<li> <b><i>"swimlane-roles"</i></b> (layout with lanes and pools based on roles, alternatively based on organizational units) and </li>
<li> <b><i>"swimlane-apps"</i></b> (layout with lanes and pools based on application systems, alternatively based on roles) </li>
</ul>
can be combined with the two <b><i>"vertical"</i></b> direction values
<ul>
<li> <b><i>true</i></b> (vertical flow, typical for EPC) and </li>
<li> <b><i>false</i></b> (horizontal flow, typical for BPMN) </li>
</ul>
and this generates six possible layouts.<br/><br/>
In addition to that the <b><i>"lcid"</i></b> defines the language, that is used in case not only a layout but also a rendering (the <b><i>"result"</i></b> type is <b><i>"esvg"</i></b>, <b><i>"svg"</i></b>, <b><i>"sesvg"</i></b> or <b><i>"ssvg"</i></b>) is requested.
</td>
</tr>
</table>

### <a name="dgm-meta-result">2.2 - The requested <code>result</code> type</a>
The currently suported ```result``` types for the ```calculation``` type ```flow``` are
* ```json``` (JSON, the recommended result type) - used in case the resulting JSON should be parsed to post-process the calculation result and log messages are not desired,
* ```ejson``` (JSON, enhanced with additional information) - used in case the resulting JSON should be parsed to post-process the calculation result and log messages are desired,
* ```esvg``` (HTML embedded SVG, enhanced with additional information) - used in case the resulting HTML has to be displayed directly (e. g. in a test app) and log messages are desired,
* ```svg``` (HTML embedded SVG, without additional information) - used in case the resulting HTML has to be displayed directly (e. g. in a test app) and log messages are not desired,
* ```sesvg``` (stand-alone SVG, enhanced with additional information) - used in case the resulting SVG has to be embedded into a custom HTML page and log messages are desired and
* ```ssvg``` (stand-alone SVG, without additional information) - used in case the resulting SVG has to be embedded into a custom HTML page and log messages are not desired

### <a name="dgm-meta-calc">2.3 - The requested <code>calculation</code> type</a>
The currently suported ```calculation``` types are
- ```cxn``` (route for a single connection path from a source node to a target node)- used to calculate one single shape-to-shape connection path and
- ```flow``` (directed graph with any number of nodes and edges)- used to calculate a directed graph, that represents a process flow.

The ```calculation``` type <code>cxn</code> supports all ```result``` types.<br/>
The ```calculation``` type <code>flow</code> supports the ```result``` types 1. ... 4. only.

### <a name="dgm-meta-conf">2.4 - The <code>configurations</code></a>
The ```configurations``` collection can currently contain one or six configuration(s).<br/>
One configuration is provided, of only one of the following layouts hast to be calculated:
- The ```layoutType``` set to ```flow``` (layout without pools / lanes) and ```vertical``` set to ```true```.
- The ```layoutType``` set to ```flow``` (layout without pools / lanes) and ```vertical``` set to ```false```.
- The ```layoutType``` set to ```swimlane-roles``` (layout with pools / lanes, lanes are based on roles or organizational units) and ```vertical``` set to ```true```.
- The ```layoutType``` set to ```swimlane-roles``` (wlayout ith pools / lanes, lanes are based on roles or organizational units) and ```vertical``` set to ```false```.
- The ```layoutType``` set to ```swimlane-apps``` (layout with pools / lanes, lanes are based on application systems and fall back to roles or organizational units) and ```vertical``` set to ```true```.
- The ```layoutType``` set to ```swimlane-apps``` (layout with pools / lanes, lanes are based on application systems and fall back to roles or organizational units) and ```vertical``` set to ```false```.

All six configurations (three ```layoutType``` values multiplied with two ```vertical``` direction values) are provided, if all of the above listed layouts have to be calculated.

## <a name="context-part">3.0 - The context part of the Request JSON (diagram as a whole)</a>
The ```context``` represents the diagram (process) to layout as a whole. BPMN contains this data within the ```<bpmndi:BPMNDiagram>``` tag.

### <a name="context-part-typic">3.1 - How it typically looks like</a>
```
  "context": {
    "id": "3783f2a0-99c0-4120-a639-4acfd59a8021",
    "properties": {
      "elementTypeKind": "business",
      "facetName": "processes",
      "state": "inProcess",
      "stereoType": "f5942ab4-2365-4a07-8a3f-04d10fb3b870",
      "type": "subProcess",
      "versionId": "2a536296-d3c7-47cf-8d2e-511eaf5b666d"
    },
    "attributes": [
      {
        "key": "links",
        "type": "linksValue",
        "values": [
          {
            "lcid": 127,
            "value": {
              "links": [
                {
                  "locator": "www.duckduckgo.com",
                  "title": "Find"
                }
              ]
            }
          }
        ]
      },
      {
        "key": "majorVersion",
        "type": "integer",
        "values": [
          {
            "lcid": 127,
            "value": 0
          }
        ]
      },
      {
        "key": "minorVersion",
        "type": "integer",
        "values": [
          {
            "lcid": 127,
            "value": 1
          }
        ]
      },
      {
        "key": "name",
        "type": "multiLineText",
        "values": [
          {
            "lcid": 1031,
            "value": "GRAPHIC-API"
          }
        ]
      }
    ]
  }
```

### <a name="context-part-info">3.2 - Context informations</a>
Typical information provided for the diagran (process) are:
* The ```id``` contains the unique identity of the diagram, it is provided for informational purposes only and is passed on to the **Result JSON**.
* The ```properties``` contains a collection of optional information.
* The ```attributes``` contains a collection of information, that are used to be displayed when the diagram is rendered. Therefore these informations are passed on to the **Result JSON**. Typically the text attributes are provided in multiple languages.

## <a name="content-part">4.0 - The <code>content</code> part of the Request JSON (containing the nodes and edges collections)</a>
The ```content``` contains the nodes and edges (in the meaning of nodes and edges within a directed graph) of the diagram (process). Typically the nodes and edges are displayed as soon als the layout is rendered. Therefore the nodes require the calculation of their positions and the edges require the calculation of their routes in order to be written to the **Result JSON** including the positions and routes.

### <a name="content-part-typic">4.1 - How it typically looks like</a>
```
  "content": {
    "nodes": [
      {
        "shapeId": "e85e124a-86aa-4fe6-aaaf-4c5707215461",
        "elementId": "5dd9be26-b592-4e02-b4b0-e508a7844e27",
        "properties": {
          "type": "evStart"
        },
        "laneRelevantRelated": [
          {
            "elementId": "c61aaf77-5d75-4ff6-a567-d9662715c46d",
            "layoutTypes": [
              "swimlane-roles",
              "swimlane-apps"
            ]
          }
        ]
      },
      ...
    ],
    "edges": [
      {
        "shapeId": "ad711e08-3fd4-44b6-bac8-518e72a005c2",
        "elementId": "3bf7c2cf-a70e-49d3-9377-301bfebec515",
        "properties": {
          "type": "crt1",
          "source": {
            "shapeId": "542e5c76-5482-4b4d-bf56-8a6d927e6a6b"
          },
          "target": {
            "shapeId": "343b15d4-3d51-4b1b-9bef-4b3b4889a88f"
          }
        }
      },
      ...
    ],
```
(see chapter [7.0 Understanding the correlation of <code>nodes</code> / <code>edges</code>  and <code>elements</code> by examples](#correlation) below).

### <a name="content-part-nodes">4.2 - The <code>nodes</code> collection</a>
Nodes represent the shapes, comparable to BPMN ```<startEvent>``` or ```<task>```.
```
      {
        "shapeId": "e85e124a-86aa-4fe6-aaaf-4c5707215461",
        "elementId": "5dd9be26-b592-4e02-b4b0-e508a7844e27",
        "properties": {
          "pool": {
            "poolId": "c84c7e2e-ea39-4fc8-b2dd-10df2b7c766a"
          },
	  "type": "evStart"
        },
        "laneRelevantRelated": [
          {
            "elementId": "c61aaf77-5d75-4ff6-a567-d9662715c46d",
            "layoutTypes": [
              "swimlane-roles",
              "swimlane-apps"
            ]
          }
        ]
      }
```
**Typical information provided for a node are:**
* The ```shapeId``` contains the unique identity of the node.
* The ```elementId``` contains the unique identity of the element, the node is based on, it is passed on to the **Result JSON**.
* The ```properties``` contains a collection of optional information, which are passed on to the **Result JSON**.
  * The optional ```pool``` contains the assignment of this node to a pool (this assignment is the same for all layouts).
  * The optional ```type``` contains the type of the node.
* The ```laneRelevantRelated``` contains a collection of elements, that are used to determine the lane the shape is to be assigned to.

**The pool assignment**

A node can optionally be assigned to a pool. The ```poolId``` must refer to an existing pool within the [The <code>pools</code> part of the Request JSON ](#pools-part).

#### <a name="content-part-nodes-lane-relevance">The <code>laneRelevantRelated</code> collection</a>
Since the **Request JSON** has to support multiple layouts at once, it is required to provide information about the lane assignment ***per layout***. Therefore the  ```laneRelevantRelated``` element collection includes for each lane relevant element the information which ```layoutTypes``` are supported by a lane relevant element. The values used for the ```layoutTypes``` here match the values used for the ```layoutType``` within the configurations.

(For details regarding the ```layoutType``` values see chapter [The <code>configurations</code> ](#dgm-meta-conf) above).

**Putting it all together**
For a sample, that shows the correlation between ```node``` and ```element``` in the Request JSON and in the Result JSON and also in comparison to BPMN see chapter [7.0 Understanding the correlation of <code>nodes</code> / <code>edges</code>  and <code>elements</code> by examples](#correlation) and section [Sample 1 (a node/shape and the underlaying element vs. the BPMN equivalent)](#correlation-sample1).

### <a name="content-part-edges">4.3 - The <code>edges</code> collection</a>
Edges represent the connection between shapes, comparable to BPMN ```<sequenceFlow>```.
```
    {
        "shapeId": "ad711e08-3fd4-44b6-bac8-518e72a005c2",
        "elementId": "3bf7c2cf-a70e-49d3-9377-301bfebec515",
        "properties": {
          "type": "crt1",
          "source": {
            "shapeId": "542e5c76-5482-4b4d-bf56-8a6d927e6a6b"
          },
          "target": {
            "shapeId": "343b15d4-3d51-4b1b-9bef-4b3b4889a88f"
          }
        }
      }
```
Typical information provided for an edge are:
* The ```shapeId``` contains the unique identity of the node.
* The ```elementId``` contains the unique identity of the element, the edge is based on, it is passed on to the **Result JSON**.
* The ```properties``` contains a collection of information, which are processed by the layouter. Every edge must contain a ```source``` ```shapeId``` and a ```target``` ```shapeId``` in order to connect two nodes.

## <a name="pools-part">5.0 - The <code>pools</code> part of the Request JSON</a>
Pools can be automatically recognized from the nodes collection.

But either if empty pools are to be added or if pools shall be sorted in a distict order the automatic pool recognition is insufficient and the pools must be listed explicitly.

So we have these cases:
* The diagram is to be laid out (and optionally rendered) without pools: The <code>pools</code> part can be left empty.
* The diagram is to be laid out (and optionally rendered) with pools but there are no specicic requirements to the pools: The <code>pools</code> part can be left empty.
* The diagram is to be laid out (and optionally rendered) with pools and an empty pool hast to be added: The <code>pools</code> part *must* contain the pools in the desired sort order.
* The diagram is to be laid out (and optionally rendered) with pools and the order of the pool has to be distinct: The <code>pools</code> part *must* contain the pools in the desired sort order.

### <a name="pools-part-typic">5.1 - How it typically looks like</a>
This is an example with three pools:

```
{
  "id": "d5d7ce44-c18f-4119-aa18-8f9e8aa69e85",
  "properties": {
    "type": "bpmnPool",
    "kind": "object",
    "servity": "source"
  },
  "attributes": [{
    "key": "name",
    "type": "multiLineText",
    "values": [
      { "lcid": 1033, "value": "Data source" }
    ]
  }]
},
{
  "id": "03c092c9-b723-4a38-befd-f6f54aef42d6",
  "properties": {
    "type": "bpmnPool",
    "kind": "object"
  },
  "attributes": [{
    "key": "name",
    "type": "multiLineText",
    "values": [
      { "lcid": 1033, "value": "Processing" }
    ]
  }],
  "children": [
      "467cbfa4-ff43-49fe-ac0d-49beda2e07f6",
      "c989af38-3e9e-4693-a284-3801da7696de"
  ]
},
{
  "id": "63cf252b-ed7f-458e-97c3-3b557786eb52",
  "properties": {
    "type": "bpmnPool",
    "kind": "object",
    "servity": "target"
  },
  "attributes": [{
    "key": "name",
    "type": "multiLineText",
    "values": [
      { "lcid": 1033, "value": "Data target" }
    ]
  }]
},
...
```
The ```"pools"``` are of **item** type ```"bpmnPool"``` and of **item** kind ```"object"```.

It is highly recommended to define names for all **pools** (see <code>"attributes"</code> with <code>"key": "name"</code>).

### <a name="pools-part-tune">5.2 - The <code>pool</code> servity</a>
One **pool** should be designated as the default **pool** so that **shapes** can be automatically (fall- back) attached to the default **pool** without explicitly assigning a **pool** to **shapes**. This creates a fail-safe behavior and makes the JSON code shorter. To identify the default **pool** you can:

- Mark all **pools** except the default **pool** as "source" (<code>"servity": "source"</code>) or "target" (<code>"servity": "target"</code>). In case of multiple matches, the last one wins.
- Mark the default **pool** (<code>"servity": "default"</code>). In case of multiple matches, the last one wins.
- In case no pool is designated as the default **pool**, the centre **pool** (count / 2) is used.

In the case that a **pool** should contain multiple **lanes** and the **lanes** should be in a defined order, it is recommended to define the **lanes** as a list of children (see <code>"children"</code>). The **ids**, specified in the list of children, must refer to existing **items**, that are typically *roles*, *groups*, *application systems* or *application services*.

Nevertheless, it is possible to provide the **pools** without any tuning at all and let the *Symbio-Graphic-Service* do all the tuning.

## <a name="elements-part">6.0 - The <code>elements</code> part of the Request JSON</a>
The <code>elements</code> collection contains all elements, that
* underlay a <code>node</code> or
* are connected to <code>nodes</code> as <code>related</code> or <code>laneRelevantRelated</code> <code>elements</code>. 

### <a name="elements-part-typic-events">6.1 - How it typically looks like for events</a>
<table>
<tr><th>An <b>event<b> sample request JSON <i>element</i></th><th>Compared to the BPMN <i>&lt;bpmn:process/&gt;</i> tag</th><th>Comments</th></tr>
<tr>
<td>

```
{
  "id":
    "B63F2971E571B69F49AAD5B0733302FA",
  "properties": {
    "type": "evStart",
    "kind": "OBJ",
    "evType": "throwSignal"
  },
  "attributes": [
    {
      "key": "name",
      "values": [
        { "lcid": 1033,
          "value": "Start" }
      ]
    },
      ...
  ]
}
```

</td>
<td>

```
<bpmn:startEvent
 id="StartEvent_0034tgy"
 name="Goods arrived">
  <bpmn:outgoing>
    SequenceFlow_0pern26
  </bpmn:outgoing>
</bpmn:startEvent>
...











```

</td>
<td>
The request JSON <i>elements</i> and the BPMN <i>&lt;bpmn:process/&gt;</i> nodes provide very similar information:<br/>
- The type, either <code>"evStart"</code> or <code>&lt;bpmn:startEvent&gt;</code>, and<br/>
- the name, either <code>"attributes"</code>/<code>"key": "name"</code> or <code>name</code> attribute.<br/><br/>
But there are also differences:<br/>
- While BPMN provides also the structure defining edges <code>&lt;bpmn:incoming&gt;</code> and <code>&lt;bpmn:outgoing&gt;</code>,<br/>
- the request JSON doesn't. This is because elements can be re-used within one request JSON and therefore a definition of incoming and outgoing edges can be ambiguous and is realized via the <code>edges</code> collection.
</td>
</tr>
</table>

### <a name="elements-part-typic-tasks">6.2 - How it typically looks like for tasks</a>
<table>
<tr><th>A <b>task<b> sample request JSON <i>element</i></th><th>Compared to the BPMN <i>&lt;bpmn:process/&gt;</i> tag</th><th>Comments</th></tr>
<tr>
<td>

```
{
  "id":
    "bcf7ab71d86145919a5a0a570fa2beed",
  "properties": {
    "type": "task",
    "kind": "OBJ",
    "typeDisplayName": "Task"
  },
  "attributes": [
    {
      "key": "name",
      "values": [
        { "lcid": 1033,
          "value": "Task-01" }
      ]
    },
    ...
  ],
  "related": [
    {
      "key": "accountableRole",
      "shortKey": "A",
      "versionIds": [
    "35f4cfb5437e4f1d8fd072859648e183"
      ]
    },
    ...
  ]
}
```

</td>
<td>

```
<bpmn:task
 id="Activity_16oto7s"
 name="Process goods">
  <bpmn:incoming>
    SequenceFlow_0pern26
  </bpmn:incoming>
  <bpmn:outgoing>
    SequenceFlow_1vdst1y
  </bpmn:outgoing>
</bpmn:task>
...


















```

</td>
<td>
The request JSON <i>elements</i> and the BPMN <i>&lt;bpmn:process/&gt;</i> nodes provide very similar information:<br/>
- The type, either <code>"task"</code> or <code>&lt;bpmn:task&gt;</code>, and<br/>
- the name, either <code>"attributes"</code>/<code>"key": "name"</code> or <code>name</code> attribute.<br/><br/>
But there are also differences:<br/>
- While BPMN provides also the structure defining edges <code>&lt;bpmn:incoming&gt;</code> and <code>&lt;bpmn:outgoing&gt;</code>,<br/>
- the request JSON doesn't. This is because elements can be re-used within one request JSON and therefore a definition of incoming and outgoing edges can be ambiguous and is realized via the <code>edges</code> collection.<br/><br/>
On the other hand the request JSON can connect a task to all types of repository <i>elements</i> via <code>"related"</code>, e. g. milestones, requirements, KPIs, documents (guidelines, directives), standards, organizational units, projects, products, objectices <i>and so on</i>.
</td>
</tr>
</table>

### <a name="elements-part-typic-roles">6.3 - How it typically looks like for roles</a>
</table>
<table>
<tr><th>A <b>role<b> sample request JSON <i>element</i></th><th>Compared to the BPMN <i>&lt;bpmn:process/&gt;</i> tag</th><th>Comments</th></tr>
<tr>
<td>

```
{
  "id":
    "ed6645df50804e6f9ab235121c5ea567",
  "versionId":
    "a2a8e8e8090f464fac78bec8b851c963",
  "properties": {
    "kind": "OBJ",
    "stereoType": "extern",
    "type": "role",
    "typeDisplayName": "Role"
  },
  "attributes": [
    {
      "key": "name",
      "values": [
        { "lcid": 1033,
          "value": "DP officer" }
      ]
    },
    ...
  ]
}
```

</td>
<td>

```
<bpmn:laneSet
  id="LaneSet_1n0pzhw">
  <bpmn:lane
    id="Lane_0w5mswf"
    name="MyLane-01">
    ...
  </bpmn:lane>
</bpmn:laneSet>
  













```

</td>
<td>
There is no general BPMN equivalent for the <i>repository</i> <code>elements</code> in request JSON.<br/><br/>
While request JSON <code>nodes</code> can refer to various types of <i>repository</i> <code>elements</code> via <code>related</code> or <code>laneRelevantRelated</code>, BPMN can only organize <code>elements</code> in swimlanes (which is equivalent to request JSON's <code>laneRelevantRelated</code>) and connect <code>elements</code> to data stores and data objects.<br/><br/>
Typically roles are used to define swimlanes - that is realizes via <code>laneRelevantRelated</code> in request JSON and <code>&lt;bpmn:laneset/&gt;</code>/<code>&lt;bpmn:lane/&gt;</code> in BPMN.<br/><br/><br/><br/>
</td>
</tr>
</table>

### <a name="elements-part-typic-data">6.4 - How it typically looks like for data</a>
</table>
<table>
<tr><th>A <b>data<b> sample request JSON <i>elements</i></th><th>Compared to the BPMN <i>&lt;bpmn:process/&gt;</i> tag</th><th>Comments</th></tr>
<tr>
<td>

```
{
  "id":
    "480439511168499d8bc5a1fda3285542",
  "versionId":
    "1129e2af0a174064a79849002e1ceb56",
  "properties": {
    "kind": "OBJ",
    "type": "system",
    "typeDisplayName": "Application"
  },
  "attributes": [
    {
      "key": "name",
      "values": [
        { "lcid": 1033,
          "value": "Accounting portal" }
      ]
    },
    ...
  ]
},
{
  "id":
    "9487278f25464394aff9e68bef9b2535",
  "versionId":
    "f1c69f8d04ad4e9e82f11f0465f536a6",
  "properties": {
    "kind": "OBJ",
    "type": "inOut",
    "typeDisplayName": "Input/Output"
  },
  "attributes": [
    {
      "key": "name",
      "values": [
        { "lcid": 1033,
          "value": "Inbound quotation" }
      ]
    },
    ...
  ]
}
```

</td>
<td>

```
<bpmn:dataStoreReference
  id=
    "DataStoreReference_07dozvu"
  name=
    "Data-Store" />
  















<bpmn:dataObjectReference
  id=
    "DataObjectReference_1unf7qz"
  name=
    "Data-Object"
  dataObjectRef=
    "DataObject_1lq9toa" />
<bpmn:dataObject
  id=
    "DataObject_1lq9toa" />











```

</td>
<td>
There is no general BPMN equivalent for the <i>repository</i> <code>elements</code> in request JSON.<br/><br/>
While request JSON <code>nodes</code> can refer to various types of <i>repository</i> <code>elements</code> via <code>related</code> or <code>laneRelevantRelated</code>, BPMN can only organize <code>elements</code> in swimlanes (which is equivalent to request JSON's <code>laneRelevantRelated</code>) and connect <code>elements</code> to data stores and data objects.<br/><br/>
Typically data stores and data objects are connected via <code>related</code> in request JSON and implemented as <code>"type": "system"</code> respectively <code>"type": "inOut"</code> in request JSON and implemented as <code>&lt;bpmn:dataStoreReference/&gt;</code> respectively <code>&lt;bpmn:dataObjectReference/&gt;</code> in BPMN.
</td>
</tr>
</table>
	
### Shapes and shape items
The design of ***shape*** content elements is intended to be minimalistic.<br/>

```
{
	"id": "a6743dd3-8881-4d22-bfe7-0b7f703097c8",
	"properties": {
            "itemId": "9bc5994a-f64f-431f-a631-1ff405f22643",
            "type": "evStart",
            "poolId": "6496cb0f-8763-4e27-a8dd-77870be94fd5"
	}
}
```

Referencing the ***shape***'s underlying ***item*** is done with <code>"itemId" : "9bc5994a-f64f-431f-a631-1ff405f22643"</code>.<br/>
The (optional) second reference, <code>"poolId": "6496cb0f-8763-4e27-a8dd-77870be94fd5"</code>, defines membership of the ***shape*** in a ***pool***.<br/>
<br/>

Here is the ***shape*** underlying ***item***:

```
{
  "id": "9bc5994a-f64f-431f-a631-1ff405f22643",
  "properties": {
    "type": "evStart",
    "kind": "OBJ",
    "evType": "throwSignal"
  },
  "attributes": [{
    "key": "name",
    "values": [
      { "lcid": 1033, "value": "recognize hunger" }
    ]
  }]
}
```

The ***type*** is provided via the <code>"properties"</code> property array property <code>"type"</code>.<br/>
In addition to that the kind (which determines whether it is a ***shape*** or a ***edge***) is provided via the <code>"properties"</code> property array property <code>"kind"</code>.<br/>
In case the ***item*** (and thus the ***shape*** as well) is defined more specifically, the <code>"properties"</code> property array might contain a property <code>"evType"</code> or <code>"funcType"</code> ore <code>"funcMarker"</code>.<br/>
The text to display is defined within the <code>"attributes"</code> property array object with the <code>"key": "name"</code>. Since the request JSON supports multiple languages, the name value is provided by the <code>"values"</code> property array, that can hold distinct value objects for different cultures.

### Edges and edge items
The design of ***edge*** content elements is intended to be minimalistic.<br/>
Here is an example:

```
{
	"id": "e5dfd46e-8720-408e-b760-171038da9f6d",
	"properties": {
		"itemId": "6496cb0f-8763-4e27-a8dd-77870be94fd5",
		"sourceId": "a6743dd3-8881-4d22-bfe7-0b7f703097c8",
		"targetId": "75397704-e829-49b3-a40c-02379fb0b393",
		"type": "activ1"
	}
}
```

Referencing the ***edge***'s underlying ***item*** is done with <code>"itemId" : "6496cb0f-8763-4e27-a8dd-77870be94fd5"</code>.<br/>
The is no reference to a ***pool***, since ***edges*** can cross ***lanes*** and ***pools***.<br/>
The referencing of the *source* and *target* ***shapes*** is done with <code>"sourceId": "a6743dd3-8881-4d22-bfe7-0b7f703097c8"</code> and <code>"targetId": "75397704-e829-49b3-a40c-02379fb0b393"</code>. An ***edge*** must always hold a *source* and a *target* ***shape*** reference.<br/><br/>

Here is the ***edge*** underlying ***item***:

```
{
    "id": "6496cb0f-8763-4e27-a8dd-77870be94fd5",
    "properties": {
        "kind": "CXN",
        "sourceId": "9bc5994a-f64f-431f-a631-1ff405f22643",
        "targetId": "b0907b38-e8e4-42f3-bc0a-d3e83ae2a1a7",
        "type": "activ1"
	},
    "attributes": [{
        "key": "cxnRole",
        "values": [
        { "lcid": 1033, "value": "yes" }
        ]
    }]
}
```

The ***type*** is provided via the <code>"properties"</code> property array property <code>"type"</code>.<br/>
In addition to that the kind (which determines whether it is a ***shape*** or a ***edge***) is provided via the <code>"properties"</code> property array property <code>"kind"</code>.<br/>
The text to display is defined within the <code>"attributes"</code> property array object with the <code>"key": "cxnRole"</code>. Since the request JSON supports multiple languages, the name value is provided by the <code>"values"</code> property array, that can hold distinct value objects for different cultures.


### Lane items
In the case that the **diagram** is to be laid out and rendered with **lanes**, the **lanes** are *automatically* recognized by the fact, that they are *related* to structure building  **items** via the configured *laneRelevant* relation (see <code>"laneRelevant": "responsible"</code> within **<code>configurations</code> property**). The  **lanes** must not be of any of any specific **item** type (see <code>"type": "..."</code>), but they must be of **item** kind "OBJ" (see <code>"kind": "OBJ"</code>) and must not be of any of the structure building  **item** types (like <code>"type": "func"</code>, <code>"type": "evStart"</code>, <code>"type": "evIntermediate"</code>, <code>"type": "evEnd"</code>, <code>"type": "ruleXor"</code>, <code>"type": "ruleOr"</code>, <code>"type": "ruleAnd"</code> or <code>"type": "condition"</code>).

In the case that multiple **lanes** should be contained in a **pool** and the **lanes** should be in a defined order, it is recommended to define the **lanes** as a list of children (see <code>"children"</code>) within the **pool**. The **ids**, specified in the list of children, must refer to existing **lane** (**items**), that are typically *roles*, *groups*, *application systems* or *application services*.








## <a name="correlation">Understanding the correlation of <code>nodes</code> / <code>edges</code>  and <code>elements</code> by examples</a>
The biggest part of the necessary information is the description of the nodes and edges. In order to understand the nodes and edges within the **Request JSON**, comparing their data to **BPMN** and the expected outcome **Result JSON** could be helpful.

**Disclaimer:** The following comparison between the *Request JSON*, *BPMN* and the *Result JSON* is made not because the two formats perform a similar task, but because they convey similar content: **Process flows**.<br/><br/>While the *Request JSON* contains only the necessary information for automatic layout calculation and rendering, the BPMN also already contains all information for rendering (like position and size of shapes or the interpolation points of edges), which comes closer to the *Result JSON*.

For basics about the nodes see section [The <code>nodes</code> collection](#content-part-nodes)</a>.

### <a name="correlation-sample1">Sample 1 (a node/shape and the underlaying element vs. the BPMN equivalent)</a>

<table>
<tr><th>Request JSON sample of a shape - no position and no size</th><th>Request JSON sample of the underlaying element</th></tr>
<tr>
<td>

```
{
  "shapeId": "fd4e1872-8742-4bfa-9c4f-b271ec81c73f",
  "elementId": "ab99ac91-be7d-43c4-955d-04324360fbdf",
  "properties": {
    "type": "evStart"
  },
  "laneRelevantRelated": [
    {
      "elementId": "c61aaf77-5d75-4ff6-a567-d9662715c46d",
      "layoutTypes": [
        "swimlane-roles",
        "swimlane-apps"
      ]
    }
  ]
}












































.
```

</td>
<td>

```
{
  "id": "ab99ac91-be7d-43c4-955d-04324360fbdf",
  "properties": {
    "kind": "object",
    "type": "evStart",
    "typeDisplayName": "Start"
  },
  "attributes": [
    {
      "key": "description",
      "type": "singleLineText",
      "values": [
        {
          "lcid": 1031,
          "value": "Goods arrived"
        }
      ]
    },
    {
      "key": "evStartTrigger",
      "type": "selectionValue",
      "values": [
        {
          "lcid": 127,
          "value": "evTriggerNoneIntSub"
        }
      ]
    },
    {
      "key": "evType",
      "type": "selectionValue",
      "values": [
        {
          "lcid": 127,
          "value": "evTypeMessage"
        }
      ]
    },
    {
      "key": "name",
      "type": "multiLineText",
      "values": [
        {
          "lcid": 1031,
          "value": "Start-01"
        }
      ]
    }
  ],
  "related": [
    {
      "key": "executiveRole",
      "shortKey": "R",
      "versionIds": [
        "9700e981-1c36-4536-a05b-592aa2fd6219"
      ]
    }
  ]
},
```

</td>
</tr>
</table>

<table>
<tr><th>BPMN sample of a shape - including position and size</th></tr>
<tr>
<td>

```
<bpmndi:BPMNShape id="IntermediateThrowEvent_0iyvpzm_di" bpmnElement="IntermediateThrowEvent_0iyvpzm">
  <dc:Bounds x="458" y="313" width="36" height="36" />
  <bpmndi:BPMNLabel>
    <dc:Bounds x="464" y="353" width="24" height="12" />
  </bpmndi:BPMNLabel>
</bpmndi:BPMNShape>
```

</td>
</tr>
</table>

<table>
<tr><th>Result SVG a shape - including position and size (and the rendering objects: rect, path and text)</th></tr>
<tr>
<td>

```
<g>
  <!-- evStart(evStart) /ID:fd4e1872-8742-4bfa-9c4f-b271ec81c73f /ItemID:ab99ac91-be7d-43c4-955d-04324360fbdf -->
  <!-- evStart /W:160 /H:160 -->
  <ellipse cx="124.7244" cy="63.49606" rx="22.677164" ry="22.677164"
    style="shape-rendering:auto; stroke:#808080; stroke-width:1px; stroke-linecap:round;
    stroke-dasharray:; stroke-opacity:1; fill:#FFFFFF; fill-opacity:1;"></ellipse>
  <!-- ##ATS##_evTypeShapeCatchMessage /W:80 /H:80 -->
  <rect x="113.5" y="55.5" rx="2.2677164" ry="2.2677164" width="22" height="16"
    style="stroke:#000000; stroke-width:1px; stroke-linecap:round; stroke-dasharray:;
    stroke-opacity:1; fill:#FFFFFF; fill-opacity:1;"></rect>
  <path d="M113.224396,57.66535L122.295265,65.602356C124.56298,66.73621 124.56298,66.73621 
    126.830696,65.602356L135.90157,57.66535" style="shape-rendering:auto; stroke:#000000;
    stroke-width:1px; stroke-linecap:round; stroke-dasharray:; stroke-opacity:1;
    fill:transparent; fill-opacity:0.0;"></path>
  <text class="" x="124.5" y="99.92084" text-anchor="middle"
    style="fill:#444444;font-family:Segoe UI;font-weight:400;font-style:normal;font-size:9pt;text-decoration:none;">
    <tspan class="" x="124.5" dy="0">Goods</tspan><tspan class="" x="124.5" dy="15">arrived</tspan></text>
</g>
```

</td>
</tr>
</table>

<table>
<tr><th>Result JSON a shape - including position and size (and the rendering objects: rect, path and text)</th></tr>
<tr>
<td>

```
{
  "id": "ab99ac91-be7d-43c4-955d-04324360fbdf",
  "properties": {
    "column": 0.0,
    "evType": "evTypeCatchMessage",
    "height": 50,
    "kind": "shape",
    "row": 0.0,
    "type": "evStart",
    "typeDisplayName": "Start",
    "width": 50,
    "x": 112.5,
    "y": 45.0
  },
  "attributes": [
    {
      "key": "evStartTrigger",
      "values": [
        { "lcid": 127, "value": "evTriggerStd" }
      ]
    },
    {
      "key": "name",
      "values": [
        { "lcid": 1033, "value": "Goods arrived" }
      ]
    }
  ],
  "attributeDisplays": [
    {
      "key": "name",
      "width": 70.0,
      "height": 34.0625,
      "xOffset": -10.0,
      "yOffset": 60.0,
      "rotation": 0.0,
      "horizontalAlignment": "center",
      "verticalAlignment": "top"
    }
  ],
  "related": [
    {
      "key": "executiveRole",
      "shortKey": "R",
      "ids": [
        "4c71d84e-2084-4dc1-bda4-dd2ff664aba4"
      ]
    }
  ]
}
```

</td>
</tr>
</table>

### Sample 2 (an edge)

For basics about the edges see section [The <code>edges</code> collection](#content-part-edges)</a>.

**BPMN sample for interpolation points of an edge**
```
<bpmndi:BPMNEdge id="SequenceFlow_1vmk1br_di" bpmnElement="SequenceFlow_1vmk1br">
  <di:waypoint xsi:type="dc:Point" x="209" y="331" />
  <di:waypoint xsi:type="dc:Point" x="245" y="331" />
  <bpmndi:BPMNLabel>
    <dc:Bounds x="182" y="310" width="90" height="12" />
  </bpmndi:BPMNLabel>
</bpmndi:BPMNEdge>
```
