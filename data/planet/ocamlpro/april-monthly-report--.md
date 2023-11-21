---
title: 'April Monthly Report  '
description: 'This post aims at summarizing the activities of OCamlPro for the past
  month. As usual, we worked in three main areas: the OCaml toolchain, development
  tools for OCaml and R&D projects. The toolchain Our multi-runtime implementation
  of OCaml had gained stability. Luca fixed a lot of low-level bugs in...'
url: https://ocamlpro.com/blog/2013_04_22_april_monthly_report
date: 2013-04-22T13:19:46-00:00
preview_image: URL_de_votre_image
featured:
authors:
- "\n    \xC7agdas Bozman\n  "
source:
---

<p>This post aims at summarizing the activities of OCamlPro for the past month. As usual, we worked in three main areas: the OCaml toolchain, development tools for OCaml and R&amp;D projects.</p>
<h2>The toolchain</h2>
<p>Our multi-runtime implementation of OCaml had gained stability. <a href="http://ageinghacker.net/">Luca</a> fixed a lot of low-level bugs in the &ldquo;master&rdquo; branch of <a href="http://www.github.com/lucasaiu/ocaml">his OCaml repository</a>, which were mainly related to the handling of signals. There are still some issues, which seem to be related to thread-switching (ie. when using OS level mutli-threading).</p>
<p>We made great progress on improved inlining strategy. In the current OCaml compiler, inlining, closure conversion and constant propagation are done in a single pass interleaved with analysis. It has served well until now, but to improve it in a way which is easily extensible in the future, it needs a complete rewrite. After a few prototypes, <a href="http://www.lsv.ens-cachan.fr/~chambart/">Pierre</a> is now coming up with a suitable intermediate language (IR) more suited for the job, using a dedicated value analysis to guide the simplification and inlining passes. This IR will stand between the lambda code and the C-lambda and is designed such that future specialized optimization can be easily be added. There are two good reasons for this IR: First, it is not as intrusive and reduces the extent of the modifications to the compiler, as it can be plugged between two existing passes and turned on or off using a command-line flag. Second, it can be tweaked to make the abstract interpretation more precise and efficient. For instance, we want the inlining to work with higher-order functions as well as modules and functors, performing basic defunctorization. It is still in an experimentation phase, but we are quickly converging on the API and hope to have something we can demo in the next months.</p>
<p>Our <a href="http://ocamlpro.com/2012/08/08/profiling-ocaml-amd64-code-under-linux/">frame-pointer patch</a> has also been accepted. Note that, as this patch changes the calling sconvention of OCaml programs, you cannot link together libraries compiled with and without the patch. Hence, this option will be provided as a configuration switch (<code>./configure --with-frame-pointer</code>).</p>
<p>Regarding memory profiling, we released a preliminary prototype of the memory profiler for native code. It is available in <a href="https://github.com/cago/ocaml">&Ccedil;agdas</a> repository. We are still in the process of testing and evaluating the prototype before making it widely available through OPAM. As the previous bytecode prototype, you need to compile the libraries and the program you want to profile as usual in order to build a table associating unique identifier to program locations (.prof file). Then, for each allocated block, we have then patched the runtime of OCaml to encode in the header the identifier of the line which allocated it. To be able to dump the heap, you can either instrument your program, or send a signal, or set the appropriate environment variable (<code>OCAMLRUNPARAM=m</code>). Finally, you can use the profiler which will read the .prof and .cmt files in order to generate a pdf file which is the amount of memory use by type. More details on this will come soon, you can already read the <a href="https://github.com/cago/ocaml/blob/4.00.1+memprof/README">README</a> file available on github.</p>
<p>Finally, we organized a new meeting with the core-team to discuss some of the bugs in the <a href="http://caml.inria.fr/mantis">OCaml bug tracker</a>. It was the first of the year, but we are now going to have one every month, as it has a very positive impact on the involvement of everybody in fixing bugs and helps focus work on the most important issues.</p>
<h2>Development Tools for OCaml</h2>
<p>Since the latest release of <a href="http://github.com/OCamlPro/ocp-indent">ocp-indent</a>, <a href="http://louis.gesbert.fr/cv.en.html">Louis</a> continued to improve the tool. We plan to release version 1.2.0 in the next couple of days, with some bug fixes (esp. related to the handling of records) and the following new features: operators are now aligned by default (as well as opened parentheses not finishing a line) and indentation can be bounded using the <code>max_indent</code> parameter. We are also using the great <a href="http://erratique.ch/software/cmdliner">cmdliner</a> which means <code>ocp-indent</code> now has nice manual pages.</p>
<p>We are also preparing a new minor release of <a href="http://opam.ocamlpro.com/">OPAM</a>, with a few bug fixes, an improved solver heuristic and improved performance. OPAM statistics seem to converge towards round numbers, as <a href="http://github.com/OCamlPro/opam">OcamlPro/opam</a> repository has recently reached 100 &ldquo;stars&rdquo; on Github, <a href="http://github.com/OCamlPro/opam-repository">OCamlPro/opam-repository</a> is not very far from being forked 100 times, while the number of unique packages on <a href="http://opam.ocamlpro.com">opam.ocamlpro.com</a> is almost 400. We are also preparing the platform release, with a cleaner and simpler client API to be used by the upcoming &ldquo;Ocamlot&rdquo;, the automated infrastructure which will test and improve the quality and consistency of OPAM packages.</p>
<p>Last, we released a very small &ndash; but already incredibly useful tool: <a href="http://github.com/OCamlPro/ocp-index">ocp-index</a>. This new tool provides completion based on what is installed on your system, with type and documentation when available. Similarly to <code>ocp-indent</code>, the main goal of this tool is to make it easy to integrate in your editor of choice. As a proof of concept, we also distribute a small curses-based tool, called <code>ocp-browser</code>, which lets you browse interactively the libraries installed on your system, as well as an emacs binding for <code>auto-complete.el</code>. Interestingly enough, behind the scene <code>ocp-index</code> uses a <a href="https://github.com/OCamlPro/ocp-index/blob/master/src/trie.mli">lazy immutable prefix tree</a> with merge operations to efficiently store and load cmis and cmt files.</p>
<h2>Other R&amp;D Projects</h2>
<p>We continued to work on the <a href="http://www.richelieu.pro/">Richelieu</a> project. We are currently adding basic type-inference for Scilab programs to our tool <a href="https://github.com/OCamlPro/richelieu/tree/jit-fabrice/scilab/modules/jit_ocaml/src/scilint">scilint</a>, to be able to print warnings on possible programers mistakes. A first part of the work was to understand how to automatically get rid of some of the <code>eval</code> constructs, especially <code>deff</code> and <code>evalstr</code> primitives that are often used. After this, <a href="https://github.com/Michaaell">Michael</a> manually analyzed some real-world Scilab programs to understand how typing should be done, and he is now implementing the type checker and a table of types for primitive functions.</p>
<p>We are also submitting a new project, called SOCaml, for funding by the French government. In 2010, <a href="http://www.ssi.gouv.fr/">ANSSI</a>, the French agency for the security of computer systems, commanded a study, called LAFOSEC, to understand the advantages of using functional languages in the domain of security. Early results of the study were presented in <a href="http://jfla.inria.fr/2013/programme">JFLA&rsquo;2013</a>, with in particular recommandations on how to improve OCaml to use it for security applications. The goal of the SOCaml project would be to implement these recommandations, to improve OCaml, to provide additional tools to detect potential errors and to implement libraries to verify marshaled values and bytecode. We hope the project will be accepted, as it opens a new application domain for OCaml, and would allow us to work on this topic with our partners in the project, such as <a href="http://www.lexifi.com">LexiFi</a> and <a href="http://michel.mauny.net/">Michel Mauny</a>&lsquo;s team at ENSTA Paristech (the project would also contribute to their <a href="http://github.com/ocaml-bytes/ocamlcc">ocamlcc</a> bytecode-to-c compiler).</p>
