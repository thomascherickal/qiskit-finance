# Qiskit Finance

[![License](https://img.shields.io/github/license/Qiskit/qiskit-finance.svg?style=popout-square)](https://opensource.org/licenses/Apache-2.0)[![Build Status](https://github.com/Qiskit/qiskit-finance/workflows/Finance%20Unit%20Tests/badge.svg?branch=master)](https://github.com/Qiskit/qiskit-finance/actions?query=workflow%3A"Finance%20Unit%20Tests"+branch%3Amaster+event%3Apush)[![](https://img.shields.io/github/release/Qiskit/qiskit-finance.svg?style=popout-square)](https://github.com/Qiskit/qiskit-finance/releases)[![](https://img.shields.io/pypi/dm/qiskit-finance.svg?style=popout-square)](https://pypi.org/project/qiskit-finance/)[![Coverage Status](https://coveralls.io/repos/github/Qiskit/qiskit-finance/badge.svg?branch=master)](https://coveralls.io/github/Qiskit/qiskit-finance?branch=master)

**Qiskit Finance** is an open-source framework that contains uncertainty components for stock/securities problems,
Ising translators for portfolio optimizations and data providers to source real or random data to
finance experiments.

* [API reference](https://qiskit.org/documentation/apidoc/qiskit_finance.html)

## Installation

We encourage installing Qiskit via the pip tool (a python package manager), which installs all
Qiskit elements, including Finance.

```bash
pip install qiskit
```

**pip** will handle all dependencies automatically and you will always install the latest
(and well-tested) version.

If you want to work on the very latest work-in-progress versions, either to try features ahead of
their official release or if you want to contribute to Finance, then you can install from source.
To do this follow the instructions in the
 [documentation](https://qiskit.org/documentation/contributing_to_qiskit.html#installing-from-source).

_**Note*: tutorials are undergoing revision and re-organization. Hence you may notice some content
you will see referenced is under legacy_tutorials pending such re-work._

----------------------------------------------------------------------------------------------------

### Creating Your First Finance Programming Experiment in Qiskit

Now that Qiskit is installed, it's time to begin working with the finance module.
Let's try an experiment using Amplitude Estimation algorithm to
evaluate a fixed income asset with uncertain interest rates.

```python
import numpy as np
from qiskit import BasicAer
from qiskit.algorithms import AmplitudeEstimation
from qiskit.circuit.library import NormalDistribution
from qiskit_finance.applications import FixedIncomeExpectedValue

# Create a suitable multivariate distribution
num_qubits = [2, 2]
bounds = [(0, 0.12), (0, 0.24)]
mvnd = NormalDistribution(num_qubits,
                          mu=[0.12, 0.24], sigma=0.01 * np.eye(2),
                          bounds=bounds)

# Create fixed income component
fixed_income = FixedIncomeExpectedValue(num_qubits, np.eye(2), np.zeros(2),
                                        cash_flow=[1.0, 2.0], rescaling_factor=0.125,
                                        bounds=bounds)

# the FixedIncomeExpectedValue provides us with the necessary rescalings
post_processing = fixed_income.post_processing

# create the A operator for amplitude estimation by prepending the
# normal distribution to the function mapping
state_preparation = fixed_income.compose(mvnd, front=True)

# Set number of evaluation qubits (samples)
num_eval_qubits = 5

# Construct and run amplitude estimation
backend = BasicAer.get_backend('statevector_simulator')
algo = AmplitudeEstimation(num_eval_qubits, state_preparation,
                            post_processing=post_processing)
result = algo.run(backend)

print('Estimated value:\t%.4f' % result.estimation)
print('Probability:    \t%.4f' % result.max_probability)
```
When running the above the estimated value result should be 2.46 and probability 0.8487.

### Further examples

Learning path notebooks may be found in the
[finance tutorials](https://qiskit.org/documentation/tutorials/finance/index.html) section
of the documentation and are a great place to start.

Jupyter notebooks containing further examples, for the finance module, may be found in the
following Qiskit GitHub repositories at 
[qiskit-community-tutorials/finance](https://github.com/Qiskit/qiskit-community-tutorials/tree/master/finance).

----------------------------------------------------------------------------------------------------

## Using a Real Device

You can also use Qiskit to execute your code on a **real quantum chip**.
In order to do so, you need to configure Qiskit to use the credentials in
your [IBM Quantum Experience](https://quantum-computing.ibm.com) account.
For more detailed information refer to the relevant instructions in the
[Qiskit Terra GitHub repository](https://github.com/Qiskit/qiskit-terra/blob/master/README.md#executing-your-code-on-a-real-quantum-chip)
.

## Contribution Guidelines

If you'd like to contribute to Qiskit, please take a look at our
[contribution guidelines](./CONTRIBUTING.md).
This project adheres to Qiskit's [code of conduct](./CODE_OF_CONDUCT.md).
By participating, you are expected to uphold this code.

We use [GitHub issues](https://github.com/Qiskit/qiskit-finance/issues) for tracking requests and bugs. Please
[join the Qiskit Slack community](https://ibm.co/joinqiskitslack)
and for discussion and simple questions.
For questions that are more suited for a forum, we use the **Qiskit** tag in [Stack Overflow](https://stackoverflow.com/questions/tagged/qiskit).

## Next Steps

Now you're set up and ready to check out some of the other examples from the
[Qiskit Tutorials](https://github.com/Qiskit/qiskit-tutorials)
repository, that are used for the IBM Quantum Experience, and from the
[Qiskit Community Tutorials](https://github.com/Qiskit/qiskit-community-tutorials).


## Authors and Citation

Finance was inspired, authored and brought about by the collective work of a team of researchers.
Finance continues to grow with the help and work of
[many people](https://github.com/Qiskit/qiskit-finance/graphs/contributors), who contribute
to the project at different levels.
If you use Qiskit, please cite as per the provided
[BibTeX file](https://github.com/Qiskit/qiskit/blob/master/Qiskit.bib).

Please note that if you do not like the way your name is cited in the BibTex file then consult
the information found in the [.mailmap](https://github.com/Qiskit/qiskit-finance/blob/master/.mailmap)
file.

## License

This project uses the [Apache License 2.0](LICENSE.txt).
