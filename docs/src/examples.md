# Examples

```julia
 using GalacticOptim, Optim
 rosenbrock(x,p) =  (p[1] - x[1])^2 + p[2] * (x[2] - x[1]^2)^2
 x0 = zeros(2)
 p  = [1.0,100.0]

 prob = OptimizationProblem(rosenbrock,x0,p)
 sol = solve(prob,NelderMead())


 using BlackBoxOptim
 prob = OptimizationProblem(rosenbrock, x0, p, lb = [-1.0,-1.0], ub = [1.0,1.0])
 sol = solve(prob,BBO())
```

Note that Optim.jl is a core dependency of GalaticOptim.jl. However, BlackBoxOptim.jl
is not and must already be installed (see the list above).

*Warning:* The output of the second optimization task (`BBO()`) is
currently misleading in the sense that it returns `Status: failure
(reached maximum number of iterations)`. However, convergence is actually
reached and the confusing message stems from the reliance on the Optim.jl output
 struct (where the situation of reaching the maximum number of iterations is
rightly regarded as a failure). The improved output struct will soon be
implemented.

The output of the first optimization task (with the `NelderMead()` algorithm)
is given below:

```julia
* Status: success

* Candidate solution
   Final objective value:     3.525527e-09

* Found with
   Algorithm:     Nelder-Mead

* Convergence measures
   √(Σ(yᵢ-ȳ)²)/n ≤ 1.0e-08

* Work counters
   Seconds run:   0  (vs limit Inf)
   Iterations:    60
   f(x) calls:    118
```
We can also explore other methods in a similar way:

```julia
 f = OptimizationFunction(rosenbrock, GalacticOptim.AutoForwardDiff())
 prob = OptimizationProblem(f, x0, p)
 sol = solve(prob,BFGS())
```
For instance, the above optimization task produces the following output:

```julia
* Status: success

* Candidate solution
   Final objective value:     7.645684e-21

* Found with
   Algorithm:     BFGS

* Convergence measures
   |x - x'|               = 3.48e-07 ≰ 0.0e+00
   |x - x'|/|x'|          = 3.48e-07 ≰ 0.0e+00
   |f(x) - f(x')|         = 6.91e-14 ≰ 0.0e+00
   |f(x) - f(x')|/|f(x')| = 9.03e+06 ≰ 0.0e+00
   |g(x)|                 = 2.32e-09 ≤ 1.0e-08

* Work counters
   Seconds run:   0  (vs limit Inf)
   Iterations:    16
   f(x) calls:    53
   ∇f(x) calls:   53
```

```julia
 prob = OptimizationProblem(f, x0, p, lb = [-1.0,-1.0], ub = [1.0,1.0])
 sol = solve(prob, Fminbox(GradientDescent()))
```
The examples clearly demonstrate that GalacticOptim.jl provides an intuitive
way of specifying optimization tasks and offers a relatively
easy access to a wide range of optimization algorithms.
