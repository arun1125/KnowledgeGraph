# Boosting
From [[XGBoost]] - This version includes the regularized term BUT in REGULAR BOOST WE DO NOT USE THE REGULARIZATION TERM

let $X$ be the data
let $y$ be the targets
let $f_i$ be the i'th model 
let $f_i(x) = \hat y_i$ be the predictions from the i'th model

## Process/Algorithm
you sequentially fit functions to your data 

$\hat y_i = \sum{f_t(x_i)}$

At each timestep t you greedily(so at each step just pick the max its [[greedy]] cause you are not considering the future, you are just picking the best right now) pick the function that minimizes this error.

**We are trying to minimize L**

$L = \sum(l(y_i, (\hat y_i)^{(t-1)} +f_t(x_i) ) ) + \theta(f_t)$

$l(x,y)$ is the loss function - it should be convex and at least twice differentiable 
$y_i$ is the true value
$(\hat y_i)^{(t-1)}$ is the prediction from the previous step
$f_t(x_i)$ is the function value at this step - **what we want to find** 
$\theta(f_t)$ is the regularization term - **Controls loss**


**Regularization**
What if we just built a single tree and over fit the data like mad to reduce the loss? 
then the regularization term would be very high. That's how things are balanced

$\theta(f_t) = \gamma T + \frac{1}{2} \lambda ||w||^2$

### How do we minimize L?
usually we would try to differentiate and set to 0 but that wont work here. So instead we find the tree at the time step that reduces the loss the most.

so finding the $f_t(x_i)$ that reduces $L = \sum(l(y_i, (\hat y_i)^{(t-1)} +f_t(x_i) ) ) + \theta(f_t)$ the most.

to find that f we need to take the taylor expansion of $l$ (thats why $l$ needed to be at least twice differentiable)

 $(*)$ $L = \sum(l(y_i, (\hat y_i)^{(t-1)}) +g_i f_t(x_i) + \frac{1}{2}h_i f^2_t(x_i)) ) + \theta(f_t)$                                     

$g_i = \delta_{y^{(t-1)}}l(y_i, \hat y^{(t-1)})$ - first derivative; gradient
$h_i = \delta^2_{y^{(t-1)}}l(y_i, \hat y^{(t-1)})$ - second derivative; hessian

We can use what we have here to find expressions for the optimal weight and best loss score for a function $f$.

$w^{*}_j = -\frac{(\sum_{i \in I_j}g_i)}{(\sum_{i \in I_j}h_i) + \lambda}$

subbing that in to $(*)$ we get 

$\hat L^{(t)}(q) = -\frac{1}{2}\sum^T_{j=1}{\frac{(\sum_{i \in I_j}g_i)^2}{(\sum_{i \in I_j}h_i) +  \lambda}} +\gamma T$
This is an expression for the optimal score of this split

you can check if a further split makes this loss better or worse. 

You split like this $I_J =I_L \cup I_R$

$L_{split} = \frac{1}{2}[\frac{(\sum_{i \in I_R}g_i)^2}{(\sum_{i \in I_R}h_i) +  \lambda} + \frac{(\sum_{i \in I_L}g_i)^2}{(\sum_{i \in I_L}h_i) +  \lambda} - \frac{(\sum_{i \in I_J}g_i)^2}{(\sum_{i \in I_J}h_i) +  \lambda}] - \gamma$

so now we have expressions for calculating if a split we create will reduce the loss. We can just greedily use this and find a solution.