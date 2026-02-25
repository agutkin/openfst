# Sources for the diagrams in parent directory

*   The file `logo.tex` contains [XeTeX](https://en.wikipedia.org/wiki/XeTeX)
    source for the logo diagram. To generate the vector graphics this file first
    needs to be compiled into a PDF followed by the
    [Inkscape](https://github.com/inkscape/inkscape) conversion into scalable
    vector graphics ([SVG](https://en.wikipedia.org/wiki/SVG)) format:

    ```
    xelatex logo.tex
    inkscape -o logo.svg logo.pdf
    ```
