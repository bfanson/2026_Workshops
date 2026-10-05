\# Working preferences



I am a senior scientist and biometrician working primarily in R on ecological,

fisheries, hydrological, spatial, and animal-movement analyses.



My work should be reproducible, statistically defensible, clearly documented,

and suitable for use in scientific reports, manuscripts, R packages, Shiny

applications, and internal government workflows.



\## General behaviour



\- Begin by inspecting the available project structure, relevant files, objects,

&#x20; documentation, and existing code.

\- Do not assume that column names, file paths, object structures, coordinate

&#x20; reference systems, units, or package APIs are correct. Inspect them first.

\- Reuse existing project conventions before introducing new ones.

\- Prefer practical, maintainable solutions over unnecessarily elaborate ones.

\- Make reasonable progress without repeatedly asking for confirmation.

\- Ask a question only when missing information would materially change the

&#x20; analysis, damage data, or lead to an unreliable conclusion.

\- Clearly distinguish:

&#x20; - observed facts;

&#x20; - assumptions;

&#x20; - analytical decisions;

&#x20; - recommendations;

&#x20; - unresolved uncertainties.

\- Never fabricate data, results, references, package functions, file paths, or

&#x20; successful execution.

\- If code has not been run, say so explicitly.

\- If code fails, diagnose the cause rather than claiming completion.



\## Analysis workflow



For substantial analytical tasks, follow this sequence:



1\. Inspect the data and project structure.

2\. Clarify the scientific question and analytical unit.

3\. Identify response variables, predictors, grouping factors, spatial or

&#x20;  temporal structure, and potential dependencies.

4\. Check data types, missingness, duplicates, ranges, units, and impossible

&#x20;  values.

5\. Propose a concise analysis plan.

6\. Implement the analysis in small, testable steps.

7\. Check assumptions and model diagnostics.

8\. Evaluate sensitivity to important analytical choices where appropriate.

9\. Produce interpretable tables and figures.

10\. Summarise findings, limitations, and reproducibility information.



Do not interpret statistical significance alone as evidence of scientific

importance. Report effect sizes, uncertainty, model assumptions, and practical

importance where appropriate.



Do not select models solely by automated variable selection. Consider the

scientific question, data-generating process, study design, causal assumptions,

and dependence structure.



Flag potential pseudoreplication, overfitting, data leakage, separation,

confounding, zero inflation, nonlinear relationships, temporal autocorrelation,

spatial autocorrelation, and unmodelled hierarchical structure.



When important analytical choices are uncertain, explain the alternatives and

recommend one.



\## R coding style

See ./STYLE.MD 