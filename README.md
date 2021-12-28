# yquant
Typesetting quantum circuits in a human-readable language

yquant is a LaTeX package that allows to quickly draw quantum circuits. It bridges the gap between the two groups of packages that already exist: those that use a logic-oriented custom language, which is then translated into TeX by means of an external program; and the pure TeX versions that mainly provide some macros to allow for an easier input.
yquant is a pure-LaTeX solution - i.e., it requires no external program - that introduces a logic oriented language and thus brings the best of both worlds together.
It builds on and interacts with TiKZ, which brings an enourmous flexibility for customization of individual circuit.

Important features in the latest updates (for a much more complete list, see the documentation):
- Support the `beamer` package (since 0.6)
- New `text` gate, which replaces the common `[draw=none] box` situation (since 0.6)
- Integration of all qpic examples in the manual, showcasing some very advanced circuits (since 0.6)
- Simple interface for circuit equations, no more `subcircuit` hassling (since 0.5)
- Measurement outputs can now directly control other gates, i.e., with a vertical classical line emanating from the measurement (since 0.4)
- Automatically adjust vertical positions of wires also for multi-register gates (since 0.4-alpha)
- Vertical alignment actually works for subcircuits (since 0.4-alpha)
- Directly support circuits written in the `qasm` language (since 0.3)
- Simple declaration of custom gates (since 0.2.1)
- Support for subcircuits, though vertical alignment may be messed up (since 0.2)
- Load circuits from files (since 0.2)
- Native support for non-contiguous multi-qubit gates (since 0.1.2)
- Extensive user-friendly support for register name ranges, lists (since 0.1.1)

A detailed reference with lots of examples is provided in the PDF version of this Readme. We will sketch some basic usage.

The arXiv runs on version 0.3.2 - please download the latest version from the releases section and include it in your submission.

Support the development:
- [![PayPal](https://img.shields.io/badge/donate-via%20PayPal-blue.svg?style=flat)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=UTR3MRBYJ825A&source=url)
- ![Bitcoin](https://img.shields.io/badge/donate-BTC-blue.svg?style=flat) 3KBFpoJuA4eSPLGXEf3jicqaV1czhK36fH
- ![Ethereum](https://img.shields.io/badge/donate-ETH-blue.svg?style=flat) 0xE0F774221290b1E41ea62c2dd9af5dbD3df7c685

## Examples
Many more examples and explanations can be found in the [PDF version](https://github.com/projekter/yquant/blob/master/doc/latex/yquant/yquant-doc.pdf) of this Readme.

### Simple teleportation circuit
![ex-01.png](https://github.com/projekter/yquant/blob/master/markdown/ex-01.png)
```LaTeX
\begin{tikzpicture}
  \begin{yquant}
    qubit {$\ket{\reg_{\idx}}$} q[3];

    h q[1];
    cnot q[2] | q[1];
    cnot q[1] | q[0];
    h q[0];
    measure q[0-1];

    z q[2] | q[1];
    x q[2] | q[0];
  \end{yquant}
\end{tikzpicture}
```

### Three-qubit phase estimation circuit with QFT and controlled-U
![ex-02.png](https://github.com/projekter/yquant/blob/master/markdown/ex-02.png)
```LaTeX
\begin{tikzpicture}
  \begin{yquant}
    qubit {$\ket{j_{\idx}} = \ket0$} j[3];
    qubit {$\ket{s_{\idx}}$} s[2];

    h j;
    box {$U^4$} (s) | j[0];
    box {$U^2$} (s) | j[1];
    box {$U$} (s) | j[2];
    h j[0];
    box {$S$} j[1] | j[0];
    h j[1];
    box {$T$} j[2] | j[0];
    box {$S$} j[2] | j[1];
    h j[2];
    measure j;
  \end{yquant}
\end{tikzpicture}
```

### Three-qubit FT QEC circuit with syndrome measurement
![ex-03.png](https://github.com/projekter/yquant/blob/master/markdown/ex-03.png)
```LaTeX
\begin{tikzpicture}
  \begin{yquant}
    qubit {$\ket{q_{\idx}}$} q[3];
    qubit {$\ket{s_{\idx}} = \ket0$} s[2];
    cbit {$c_{\idx} = 0$} c[2];

    h s[0];
    cnot s[1] | s[0];
    cnot s[0] | q[0];
    cnot s[1] | q[1];
    cnot s[1] | s[0];
    h s[0];
    measure s;
    cnot c[0] | s[0];
    cnot c[1] | s[1];
    discard s;

    init {$\ket0$} s;
    h s[0];
    cnot s[1] | s[0];
    cnot s[0] | q[1];
    cnot s[1] | q[2];
    cnot s[1] | s[0];
    h s[0];
    measure s;

    box {Process\\Syndrome} (s, c);
    box {$\mathcal R$} (q) | s, c;
  \end{yquant}
\end{tikzpicture}
```

### Error correction
![ex-04.png](https://github.com/projekter/yquant/blob/master/markdown/ex-04.png)
```LaTeX
% \usetikzlibrary{quotes}
\begin{tikzpicture}
  \begin{yquant}
    qubit {} msg[3];
    nobit syndrome[3];

    [this subcircuit box style={dashed, "Syndrome Measurement"}]
    subcircuit {
       qubit {} msg[3];
       [out]
       qubit {$\ket0$} syndrome[3];

       cnot syndrome[0] | msg[0];
       cnot syndrome[0] | msg[1];
       cnot syndrome[1] | msg[1];
       cnot syndrome[1] | msg[2];
       cnot syndrome[2] | msg[0];
       cnot syndrome[2] | msg[2];

       dmeter {$M_{\symbol{\numexpr`a+\idx}}$} syndrome;
    } (msg[-2], syndrome[-2]);

    ["Recovery"]
    box {$\mathcal R$} (msg) | syndrome;
    discard syndrome;
  \end{yquant}
\end{tikzpicture}
```

### Lots of controls
![ex-05.png](https://github.com/projekter/yquant/blob/master/markdown/ex-05.png)
```LaTeX
\begin{tikzpicture}
   \begin{yquant*}
      zz (a[0, 2]);
      cnot a[1] ~ a[0];
      zz (a[2, 3]);
      h a[3] | a[0] ~ a[1];
      measure a[2, 3];
      box {$U$} (a[0, 1]) | a[3] ~ a[2];
      discard a[2, 3];
   \end{yquant*}
\end{tikzpicture}
```

### Circuit equations
![ex-06.png](https://github.com/projekter/yquant/blob/master/markdown/ex-06.png)
```LaTeX
% \useyquantlanguage{groups}
\begin{tikzpicture}
   \begin{yquantgroup}
      \registers{
         qubit {} q[2];
      }
      \circuit{
         h -;
         cnot q[1] | q[0];
         h -;
      }
      \equals
      \circuit{
         cnot q[0] | q[1];
      }
   \end{yquantgroup}
\end{tikzpicture}
```