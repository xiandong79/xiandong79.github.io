**Motivation**. In this section we will develop expertise with an intuitive understanding of backpropagation, which is a way of computing gradients of expressions through recursive application of chain rule. Understanding of this process and its subtleties is critical for you to understand, and effectively develop, design and debug Neural Networks.

**Problem statement**. The core problem studied in this section is as follows: We are given some function f(x) is a vector of inputs and we are interested in computing the gradient of f at x (i.e. ∇f(x) ).

### Simple expressions and interpretation of the gradient

<math xmlns="http://www.w3.org/1998/Math/MathML" display="block">
  <semantics>
    <mrow>
      <mfrac>
        <mrow>
          <mi>d</mi>
          <mi>f</mi>
          <mo stretchy="false">(</mo>
          <mi>x</mi>
          <mo stretchy="false">)</mo>
        </mrow>
        <mrow>
          <mi>d</mi>
          <mi>x</mi>
        </mrow>
      </mfrac>
      <mo>=</mo>
      <munder>
        <mo movablelimits="true" form="prefix">lim</mo>
        <mrow class="MJX-TeXAtom-ORD">
          <mi>h</mi>
          <mtext>&#xA0;</mtext>
          <mo stretchy="false">&#x2192;<!-- → --></mo>
          <mn>0</mn>
        </mrow>
      </munder>
      <mfrac>
        <mrow>
          <mi>f</mi>
          <mo stretchy="false">(</mo>
          <mi>x</mi>
          <mo>+</mo>
          <mi>h</mi>
          <mo stretchy="false">)</mo>
          <mo>&#x2212;<!-- − --></mo>
          <mi>f</mi>
          <mo stretchy="false">(</mo>
          <mi>x</mi>
          <mo stretchy="false">)</mo>
        </mrow>
        <mi>h</mi>
      </mfrac>
    </mrow>
    <annotation encoding="application/x-tex">\frac{df(x)}{dx} = \lim_{h\ \to 0} \frac{f(x + h) - f(x)}{h}</annotation>
  </semantics>
</math>


> The derivative on each variable tells you the sensitivity of the whole expression on its value.

### Compound expressions with chain rule

The chain rule tells us that the correct way to “chain” these gradient expressions together is through multiplication. For example, <math xmlns="http://www.w3.org/1998/Math/MathML">
  <semantics>
    <mrow>
      <mfrac>
        <mrow>
          <mi mathvariant="normal">&#x2202;</mi>
          <mi>f</mi>
        </mrow>
        <mrow>
          <mi mathvariant="normal">&#x2202;</mi>
          <mi>x</mi>
        </mrow>
      </mfrac>
      <mo>=</mo>
      <mfrac>
        <mrow>
          <mi mathvariant="normal">&#x2202;</mi>
          <mi>f</mi>
        </mrow>
        <mrow>
          <mi mathvariant="normal">&#x2202;</mi>
          <mi>q</mi>
        </mrow>
      </mfrac>
      <mfrac>
        <mrow>
          <mi mathvariant="normal">&#x2202;</mi>
          <mi>q</mi>
        </mrow>
        <mrow>
          <mi mathvariant="normal">&#x2202;</mi>
          <mi>x</mi>
        </mrow>
      </mfrac>
    </mrow>
    <annotation encoding="application/x-tex"></annotation>
  </semantics>
</math>. In practice this is simply a multiplication of the two numbers that hold the two gradients. 

### Intuitive understanding of backpropagation

>This extra multiplication (for each input) due to the chain rule can turn a single and relatively useless gate into a cog in a complex circuit such as an entire neural network.

![circuit_diagram.png](/downloads/circuit_diagram.png)

* Lets get an intuition for how this works by referring again to the example. The add gate received inputs [-2, 5] and computed output 3. Since the gate is computing the addition operation, its local gradient for both of its inputs is +1. The rest of the circuit computed the final value, which is -12. 

* During the backward pass in which the chain rule is applied recursively backwards through the circuit, the add gate (which is an input to the multiply gate) learns that the gradient for its output was -4. If we anthropomorphize the circuit as wanting to output **a higher value** (which can help with intuition), then we can think of the circuit as “wanting” the output of the add gate to be lower (due to negative sign), and with a force of 4. 

* To continue the recurrence and to chain the gradient, the add gate takes that gradient and multiplies it to all of the local gradients for its inputs (making the gradient on both x and y 1 * -4 = -4). Notice that this has the desired effect: If x,y were to decrease (responding to their negative gradient) then the add gate’s output would decrease, which in turn makes the multiply gate’s output increase.

Backpropagation can thus be thought of as gates communicating to each other (through the gradient signal) whether they want their outputs to increase or decrease (and how strongly), so as to make the final output value higher.

### Modularity: Sigmoid example

<math xmlns="http://www.w3.org/1998/Math/MathML" display="block">
  <semantics>
    <mrow>
      <mi>f</mi>
      <mo stretchy="false">(</mo>
      <mi>w</mi>
      <mo>,</mo>
      <mi>x</mi>
      <mo stretchy="false">)</mo>
      <mo>=</mo>
      <mfrac>
        <mn>1</mn>
        <mrow>
          <mn>1</mn>
          <mo>+</mo>
          <msup>
            <mi>e</mi>
            <mrow class="MJX-TeXAtom-ORD">
              <mo>&#x2212;<!-- − --></mo>
              <mo stretchy="false">(</mo>
              <msub>
                <mi>w</mi>
                <mn>0</mn>
              </msub>
              <msub>
                <mi>x</mi>
                <mn>0</mn>
              </msub>
              <mo>+</mo>
              <msub>
                <mi>w</mi>
                <mn>1</mn>
              </msub>
              <msub>
                <mi>x</mi>
                <mn>1</mn>
              </msub>
              <mo>+</mo>
              <msub>
                <mi>w</mi>
                <mn>2</mn>
              </msub>
              <mo stretchy="false">)</mo>
            </mrow>
          </msup>
        </mrow>
      </mfrac>
    </mrow>
    <annotation encoding="application/x-tex">f(w,x) = \frac{1}{1+e^{-(w_0x_0 + w_1x_1 + w_2)}}</annotation>
  </semantics>
</math>

![circuit.png](/downloads/circuit.png)

