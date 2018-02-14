---
layout: post
title:  "How to Wrap Polymorphic C++ Implementations with Cython"
date:   2018-02-14 13:24:00 -0500
permalink: how-to-wrap-polymorphic-cpp-classes-with-cython
tags:
  - programming
  - python
  - c++
  - cython
---

### Why you should even bother learning about Cython? ###
<a href="http://cython.org/" target="_blank">Cython</a> is one of the most useful advanced tools you should consider adding to your Python skillset, if you have not already. Especially in the case of complex mathematical programs with haevy computation, C++ is still a very good option for an efficient program. The easiest way to go about it is to have C++ do the heavy lifting and have a Python wrapper layer to easily access and call the lower-level C++ implementation. Cython does just that; it helps you make a C/C++ or Python implementation callable from the other language. I honestly can't think of a use-case where wants to wrap Python and interact with it using C++, but the other way around is applicable for complex computations as I mentioned. I will only talk about wrapping C++ with Cython because that's what I am experienced with.

Basically, Cython is a programming language that is a hybrid of C/C++ and Python. It lets you interact and import implementations from both languages and outputs a shared object file, with an ".so" extension which you can import like a class from Python. If you are just getting started and think this sounds very confusing, which I completely understand, go check out this wonderful <a href="https://www.youtube.com/watch?v=gMvkiQ-gOW8" target="_blank">introductory talk</a>. Yes, I know it is 3.5+ hours long, but I have watched it at least twice and it is worth every second. I thought about doing a comprehensive tutorial for Cython, but I honestly don't think I can do a better job than he did. So, I am only covering the polymorphic implementations as an extension.

### Cython for Polymorphic Code###
The examples in the talk are pretty detailed and possibly detailed enough to be used as a template for your purposes. However, it does not cover wrapping a set of polymorphic C++ classes which was what I needed to do 3 years ago. I couldn't find any resources on how to do this, so I ended up trying to wrap combinations of classes and parent functions, etc. and finally did it through trial and error. If that's something you also need to do, buckle up becaus this is going to be a wild ride.

Without getting too much into detail, I had to wrap an approximation algorithm called TIM+ implemented in C++ with lots of inheritance to use as a subroutine in our own algorithm for a research project. Our algorithm did not involve much computational complexity except for TIM+ subroutine, so I obviously implemented the rest in Python. TIM+ was not a gigantic library, but there were many functions that are called only internally that I didn't need. The easiest way to wrap a polymorphic library is simply wrapping every class fully and be done with it, but that would be very redundant in my case because I only needed access to one function and one variable from Python. So, the question is to figure out how small portion of the code you can wrap and still have Cython work properly.

Before getting into details, you can find the full .cpp and .h files and wrapper scripts <a href="https://github.com/altugkarakurt/OCIMP/tree/master/TIM%2B/Tim" target="_blank">here</a> if you want to copy-paste or look into more details. `Graph` is the base class, `InfGraph` extends `Graph` and finally `TimGraph` extends `InfGraph`. All I need is to access a functions and an attribute of `InfGraph` and a function of `TimGraph`. The stuff I need look like this:

:
```c++
class Graph{
    public:
        /*Some other declarations*/
        Graph(string graph_file, int node_cnt, int edge_cnt, int seed_size, string model);
};

class InfGraph: public Graph{
    public:
        /*Some other declarations*/
        set<int> seedSet;
        InfGraph(string graph_file, int node_cnt, int edge_cnt, int seed_size, string model);
        double InfluenceHyperGraph();
};

class TimGraph: public InfGraph{
    public:
        /*Some other declarations*/
        TimGraph(string graph_file, int node_cnt, int edge_cnt, int seed_size,string model);
        void EstimateOPT(double epsilon);
};
```

To access any implementation of a child class, I had to wrap all parent classes individually, too. So, I first wrapped `Graph` and `InfGraph` classes and had to wrap a few of their functions to test if everything returned the same results as in their respective C++ implementations. After making sure that the classes are wrapped properly, I got rid of all the functions that I won't really use and ended up with only wrapping their constructors. The good thing is, despite `seedSet` and `InfluenceHyperGraph` being implemented in its parent class, I can wrap them as part of `TimGraph`. This is pretty intuitive, but it was very helpful to test warpped functions of a single class rather than worrying about multiple classes. In the end, I ended up wrapping the constructors of all parent classes and accessed all the attributes and functions from the leaf class rather than their respective classes. The final `.pyx` file was the following.

```python
from libcpp.string cimport string
from libcpp.vector cimport vector
from libcpp.set cimport set

cdef extern from "Graph.h":
    cdef cppclass Graph:
        Graph(string, int, int, int, string) except +

cdef extern from "InfGraph.h":
    cdef cppclass InfGraph(Graph):
        InfGraph(string, int, int, int, string) except +

cdef extern from "TimGraph.h":
    cdef cppclass TimGraph(InfGraph):
        TimGraph(string, int, int, int, string) except +
        void EstimateOPT(double)
        double InfluenceHyperGraph()
        set[int] seedSet

cdef class PyGraph:
    cdef Graph *thisptr

    def __cinit__(self, string graph_file, int node_cnt, int edge_cnt, int seed_size, string model):
        self.thisptr = new Graph(graph_file, node_cnt, edge_cnt, seed_size, model)

    def __dealloc__(self):
        del self.thisptr

cdef class PyInfGraph(PyGraph):
    cdef InfGraph *thisinfptr

    def __cinit__(self, string graph_file, int node_cnt, int edge_cnt, int seed_size, string model):
        self.thisinfptr = new InfGraph(graph_file, node_cnt, edge_cnt, seed_size, model)

    def __dealloc__(self):
        del self.thisinfptr

cdef class PyTimGraph(PyInfGraph):
    cdef TimGraph *thistimptr

    def __cinit__(self, string graph_file, int node_cnt, int edge_cnt, int seed_size, string model):
        self.thistimptr = new TimGraph(graph_file, node_cnt, edge_cnt, seed_size, model)

    def __dealloc__(self):
        del self.thistimptr

    def get_seed_set(self, double epsilon=0.1):
        self.thistimptr.EstimateOPT(epsilon)
        return self.thistimptr.seedSet

    def get_influence(self):
        return self.thistimptr.InfluenceHyperGraph()
```

and the accompanying wrapping script `setup.py` is below.

```python
from distutils.core import setup, Extension
from Cython.Distutils import build_ext
from distutils.extension import Extension

sources_list = ["timgraph.pyx", "Graph.cpp", "InfGraph.cpp", "TimGraph.cpp", "sfmt/SFMT.c"]

setup(ext_modules=[Extension("pytim", sources=sources_list,language="c++",extra_compile_args=["-std=c++11"])],cmdclass={'build_ext':build_ext})
```