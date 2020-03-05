# Assign2_comp2
// CPCS 324 Algorithms & Data Structures 2
// Graph data structure starter - First Edge Object
// 2019, Dr. Muhammad Al-Hashimi

// -----------------------------------------------------------------------
// simple graph object with linked-list edge implementation and minimal fields
// extra vertex and edge member fields and methods to be added later as needed
//

var _v = [], _e = [];   // globals used by standard graph reader method


// -----------------------------------------------------------------------
// global caller function, a main() for the caller page
// only function allowed to access global vars

function _main()
{
   // create a graph (default undirected)
   var g = new Graph();

   // set input graph properties (label, directed etc.)
   g.label = "Figure 3.10 (Levitin, 3rd edition)";

   // use global input arrays _v and _e to initialize its internal data structures
   g.read_graph(_v,_e);

   // use print_graph() method to check graph
   g.print_graph();

   // report connectivity status if available
   document.write(g.connectedComp == 0 ? '<br> no connectivity info' : "DISCONNECTED ".concat(g.connectedComp));

   // perform depth-first search and output stored result
   g.topoSearch('DFS');
   document.write("<p>dfs_push: ", g.dfs_push, "</p>");

   // report connectivity status if available
   document.write(g.connectedComp == 0 ? '<br> no connectivity info' : "DISCONNECTED ".concat(g.connectedComp));

   // perform breadth-first search and output stored result
   g.topoSearch('BFS');
   document.write("<p>bfs_out: ", g.bfs_out, "</p>");

   // output the graph adjacency matrix
   document.write("<p>first row matrix: ", g.adjacencyMatrix()[0], "</p>");
   document.write("<p>last row matrix: ", g.adjacencyMatrix()[g.nv-1], "</p>");

}


// -----------------------------------------------------------------------
// Vertex object constructor

function Vertex(v)
{
   // user input fields

   this.label = v.label;          // vertex can be labelled

   // more fields to initialize internally

   this.visit = false;            // vertex can be marked visited or "seen"
   this.adjacent = new List();    // init an adjacency list

   // --------------------
   // member methods use functions defined below

   this.adjacentById = adjacentById;         // return target id of incident edges in array

}

// -----------------------------------------------------------------------
// Edge object constructor
function Edge(target_v)
{
   this.target_v = target_v;
}


// -----------------------------------------------------------------------
// Graph object constructor

function Graph()
{
   this.vert = [];                // vertex list (an array of Vertex objects)
   this.nv;                       // number of vertices
   this.ne;                       // number of edges
   this.digraph = false;          // true if digraph, false otherwise (default undirected)
   this.dfs_push = [];            // DFS order output
   this.bfs_out = [];             // BFS order output
   this.label = "";               // identification string to label graph

   // --------------------
   // student property fields next

   this.connectedComp = 0;                             // number of connected comps set by DFS; 0 (default) for no info
   this.adjacencyMatrix = makeAdjMatrix;                   // graph adjacency matrix to be created on demand


   // --------------------
   // member methods use functions defined below

   this.read_graph = better_input;   // default input reader method
   this.print_graph = better_output; // better printer function
   this.list_vert = list_vert;

   this.add_edge = add_edge;        // replace (don't change old .add_edge)
   this.add_edge2 = add_edge2;
   this.dfs = dfs;                  // DFS a connected component
   this.bfs = bfs;                  // BFS a connected component

   // --------------------
   // student methods next; implementing functions in student code section at end

   this.topoSearch = topoSearch;           // perform a topological search


}


// -------------------------------------------------------
// Functions used by methods of Graph object. Similar to
// normal functions but use object member fields and
// methods, depending on which object is passed by the
// method call through the self variable: this.
//

// --------------------
function list_vert()
{
   var i, v;  // local vars
   for (i=0; i < this.nv; i++)
   {
      v = this.vert[i];
      document.write( "VERTEX: ", i, " {", v.label, "} - VISIT: ", v.visit,
         " - ADJACENCY: ", v.adjacentById(), "<br>" );
   }
}

// --------------------
function better_input(v,e)
{
   var i;

   this.nv = v.length;
   this.ne = e.length;

   for (i = 0; i < v.length; i++)
   {
      this.vert[i] = new Vertex(v[i]);
   }

   for (i = 0; i < this.ne; i++)
   {
      this.add_edge2(e[i].u, e[i].v);
   }

   if (!this.digraph)
   {
      this.ne = e.length * 2;
   }
}

// --------------------
function better_output()
{
   document.write("<p>GRAPH {",this.label, "} ", this.digraph?"":"UN", "DIRECTED - ", this.nv, " VERTICES, ",
      this.ne, " EDGES:</p>");

   // list vertices
   this.list_vert();
}

// --------------------
function add_edge(u_i,v_i)   // obsolete, replaced by add_edge2() below
{

}

// --------------------
function dfs(v_i)
{
   // get landing vert by id then process
   var v_id = this.vert[v_i];
   v_id.visit = true;
   push_index = this.dfs_push.length;
   this.dfs_push[push_index] = v_i;

   // recursively traverse unvisited adjacent vertices
   var list = v_id.adjacentById();

   for ( var j = 0; j < list.length; j++ )
	{
		if (!this.vert[list[j]].visit)
		{
			this.dfs(list[j]);
		}
    }
    //pop_index = this.dfs_pop.length;
    //this.dfs_pop[pop_index] = v_i;
}

// --------------------
function bfs(v_i)
{
   // get vertex v by its id
   var v = this.vert[v_i];

   // process v 
   v.visit = true;

   // initialize queue with v
   var queue = new Queue();
   queue.enqueue(v_i);
    

   // while queue not empty
   while (!queue.isEmpty())
   {

       // dequeue and process a vertex, u
       var u_i = queue.dequeue();
     var u = this.vert[u_i];
     bfs_index = this.bfs_out.length;
       this.bfs_out[bfs_index] = u_i;    


       // queue all unvisited vertices adjacent to u
       var adj = u.adjacentById();
       for (var i = 0; i < adj.length; i++)
       {
        var w = adj[i];
           if (!this.vert[w].visit)
           {
               this.vert[w].visit = true;
               queue.enqueue(w);
           }
       }
   }
}


// -----------------------------------------------------------------------
// -----------------------------------------------------------------------
// --- begin student code section ----------------------------------------

function adjacentById()
{
   var traverse = this.adjacent.traverse();
   var adjacent_array = [];

   for(var i=0; i<traverse.length; i++){
      adjacent_array[i] = traverse[i].target_v;      
   }
   return adjacent_array;
}

// --------------------
function add_edge2(u_i,v_i)
{
    // fetch vertices using their id, where u: edge source vertex, v: target vertex
    u = this.vert[u_i];
    v = this.vert[v_i];


   // insert (u,v), i.e., insert v in adjacency list of u
   // (first create edge object using v_i as target, then pass object)
   var v_e = new Edge(v_i);
   u.adjacent.insert(v_e);


   // insert (v,u) if undirected graph (repeat above but reverse vertex order)
   if(!this.digraph)
   {
      var u_e = new Edge(u_i);
      v.adjacent.insert(u_e);
   }

}

// --------------------
function topoSearch(search)
{
   for (var i = 0; i < this.nv; i++)
   {
      this.vert[i].visit = false;
   }

   for (var i = 0; i < this.nv; i++)
   {
      if (!this.vert[i].visit)
      {
         search == 'DFS'? this.dfs(i): this.bfs(i);
         this.connectedComp++;
      }
   }
}

// --------------------
function makeAdjMatrix()
{
   // initially create row elements and zero the adjacency matrix
   var adjMatrix = [];  
   for(var i = 0; i < this.nv; i++){
      adjMatrix[i] = [];
      for(var j = 0; j < this.nv; j++){
         adjMatrix[i][j] = 0;
      }
   }

   // for each vertex, set 1 for each adjacency
   for(var i = 0; i < this.nv; i++){
      x = this.vert[i].adjacentById();
      for(var j = 0; j < x.length; j++){
         adjMatrix[i][x[j]] = 1;
      }
   }
   return adjMatrix;
}
