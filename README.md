% LICENSE NOTICE: 
% This work is licensed under a Creative Commons Attribution 4.0 International License.
% To view a copy of this license, visit http://creativecommons.org

\section{Conversion of Clauses to Implications with AND Gates}
A 3-variable clause ($A \lor B \lor C$) can be broken down into equivalent implication statements using only AND gates by applying De Morgan's laws:
\begin{itemize}
    \item \textbf{Group 1 (Implications with AND gates as inputs):}
    \begin{itemize}
        \item $(\neg A \land \neg B) \rightarrow C$
        \item $(\neg A \land \neg C) \rightarrow B$
        \item $(\neg B \land \neg A) \rightarrow C$
        \item $(\neg B \land \neg C) \rightarrow A$
        \item $(\neg C \land \neg A) \rightarrow B$
        \item $(\neg C \land \neg B) \rightarrow A$
    \end{itemize}
    \item \textbf{Group 2 (Implications with OR gates converted to AND gates):}
    \begin{itemize}
        \item $\neg C \rightarrow \neg(\neg A \land \neg B)$
        \item $\neg A \rightarrow \neg(\neg B \land \neg C)$
        \item $\neg B \rightarrow \neg(\neg A \land \neg C)$
    \end{itemize}
\end{itemize}

\section{2-Variable Substitution Assumption}
Each distinct AND gate block is assumed to be a single substitute variable (referred to as a macro-switch):
\begin{itemize}
    \item Let $X = (\neg A \land \neg B)$
    \item Let $Y = (\neg B \land \neg C)$
    \item Let $Z = (\neg A \land \neg C)$
\end{itemize}
By substituting these macro-variables, the complex 3-variable rules are compacted into structural placeholders fit for directed graph representation.

\section{Running Tarjan's Algorithm on the Implication Graph}
We construct a directed graph $G = (V,E)$ where the vertices $V$ represent the literal variables and the macro-switches ($A,B,C,X,Y,Z$ and their respective negations), and the edges $E$ represent the implication arrows. Tarjan's algorithm is run on this directed graph in linear time $O(V + E)$ to isolate all Strongly Connected Components (SCCs).

\section{Macro-Variable Expansion and Pre-Assignment Substitution}
Once Tarjan's algorithm groups the vertices into initial strongly connected components, the macro-switches must be fully resolved. Before performing any contradiction checks or evaluating assignments, every macro-switch located inside an SCC is structurally expanded into its underlying micro-variable definition.

For example, if a macro-switch is defined as:
\[ X = (\neg A \land B) \]
Substituting the definition of $X$ back into its component group flattens the SCC, populating it directly with its constituent micro-variables:
\[ \dots, \neg A, B, \dots \]
This pre-assignment expansion strips away the structural placeholders so that the core micro-variables govern the final assessment.

\section{Contradiction Checking and 2-SAT Value Assignment}
Following the flattening of all components, the graph's satisfiability is determined by evaluating the micro-variables in the exact manner of a standard 2-SAT workflow:
\begin{enumerate}
    \item \textbf{Flattened Contradiction Check:} The expanded components are checked for explicit logic violations. If any component contains both a base micro-variable and its immediate literal complement simultaneously (e.g., both $A$ and $\neg A$ wind up in the same group due to macro-expansion), a direct logical contradiction is triggered. The system flags this state as UNSAT.
    \item \textbf{Reverse Topological Assignment:} If no contradictions are found, a satisfying assignment is guaranteed. The components are traversed in reverse topological order (from sinks to sources, matching the natural finish-time output of Tarjan's algorithm):
    \begin{itemize}
        \item The micro-variables inside the current unassigned component are greedily assigned a truth value of True.
        \item Their complementary literals residing in the conjugate component are automatically assigned False.
    \end{itemize}
\end{enumerate}
Because the macro-switches are entirely expanded into their micro-variables prior to this phase, the resulting truth assignments inherently satisfy all underlying 3-SAT clause dependencies without requiring secondary macro-validation.
