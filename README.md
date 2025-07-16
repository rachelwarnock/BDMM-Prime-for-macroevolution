---
author: Rachel C. M. Warnock, Timothy G Vaughan
level: Intermediate
title: BDMM-Prime for Macroevolution
subtitle: The fossilized birth-death process and extensions
beastversion: 2.7.7
---

# Background

The fossilized birth-death (FBD) process is a phylodynamic model that incorporates sampling through time and allows us to infer trees with sampled ancestors {% cite Stadler2010 Heath2014 --file BDMM-Prime-for-macroevolution/master-refs.bib %}. This is important for phylogenies that incorporate fossils. In the original formulation of the model, the birth (speciation), death (extinction), and fossil sampling rates were treated as constant over time and across the tree. Since then, many model extensions have been published that relax the assumption of constant rates or uniform sampling (see {% cite Mulvey2025a --file BDMM-Prime-for-macroevolution/master-refs.bib %} for an overview). The BEAST2 package BDMM-Prime {% cite Vaughan2025 --file BDMM-Prime-for-macroevolution/master-refs.bib %} incorporates several of these new models, including those that allow rates to vary across time or across different parts of the tree {% cite Gavryushkina2014 Kuhnert2016 Scire2022 --file BDMM-Prime-for-macroevolution/master-refs.bib %}. 

This tutorial will provide an introduction of how the BDMM-Prime package can be applied in macroevolution. For an example in epidemiology see the [BDMM-Prime package website](https://tgvaughan.github.io/BDMM-Prime/#id-1-Introduction).

## Pre-requisites

This tutorial assumes that you have already gained some experience using BEAST2. If you have not already done so, we recommend first completing the following tutorials: 

- [Introduction to BEAST2](https://taming-the-beast.org/tutorials/Introduction-to-BEAST2/)
- [Dating species divergences with the fossilized birth-death process](https://taming-the-beast.org/tutorials/FBD-tutorial/) or [Total-evidence dating using BEAST2](https://taming-the-beast.org/tutorials/Total-Evidence-Tutorial/)

----

# Programs used in this exercise 

### BEAST2 - Bayesian Evolutionary Analysis Sampling Trees 2

BEAST2 is a free software package for Bayesian evolutionary analysis of molecular sequences using MCMC and strictly oriented toward inference using rooted, time-measured phylogenetic trees {% cite Bouckaert2014 Bouckaert2019 --file BDMM-Prime-for-macroevolution/master-refs.bib %}. This tutorial uses BEAST v{{ page.beastversion }}.

### BEAUti - Bayesian Evolutionary Analysis Utility

BEAUti2 is a graphical user interface tool for generating BEAST2 XML configuration files.

Both BEAST2 and BEAUti2 are Java programs, which means that the exact same code runs, and the interface will be the same, on all computing platforms. The screenshots used in this tutorial are taken on a Mac OS X computer; however, both programs will have the same layout and functionality on both Windows and Linux. BEAUti2 is provided as a part of the BEAST2 package so you do not need to install it separately.


<!-- Removing for now, it is only used to open the nexus file not to edit anything...
### Any programmer-friendly text editor

We will need to view our data input files to understand their format, for which we'll need a text editor. It's best to use one designed for programmers as these include nice features such as syntax highlighting, which makes the code more reader-friendly. [Sublime Text](https://www.sublimetext.com) is a good option which is available for MacOS, Windows and Linux.

-->

### Tracer

[Tracer](http://beast.community/tracer) is used to summarise the posterior estimates of the various parameters sampled by the Markov Chain. This program can be used for visual inspection and to assess convergence. It helps to quickly view median estimates and 95% highest posterior density intervals of the parameters, and calculates the effective sample sizes (ESS) of parameters. It can also be used to investigate potential parameter correlations. We will be using Tracer v{{ page.tracerversion }}.

### TreeAnnotator

TreeAnnotator is used to summarise the posterior sample of trees to produce a maximum clade credibility tree and summarize the posterior estimates of other parameters that can be easily visualized on the tree (e.g., node height). This program is also useful for comparing a specific tree topology and branching times to the set of trees sampled in the MCMC analysis.

### IcyTree

IcyTree ([https://icytree.org](https://icytree.org)) is a browser-based phylogenetic tree viewer. It is intended for rapid visualisation of phylogenetic tree files. It can also render phylogenetic networks provided in extended Newick format. IcyTree is compatible with current versions of Mozilla Firefox and Google Chrome.

----

# Practical: BDMM-Prime for Macroevolution

BDMM-Prime is a package written for flexible phylodynamic inference. It combines the functionality of multiple other BEAST2 packages, including the BDMM, BDSKY, and SA packages, and can be applied to a wide range of scenarios in epidemiology and macroevolution. Due to the flexibility and the large variety of ways in which model parameters can be combined using BDMM-Prime there are more options to consider when setting up your analysis.

## The data

We'll use a published dataset of [amniotes](https://en.wikipedia.org/wiki/Amniote) that has been used previously to explore the early evolution of this group {% cite Bouckaert2014 --file BDMM-Prime-for-macroevolution/master-refs.bib %}.

The data comprises 294 binary and multi-state characters for 70 fossils, as well as their age and locality information, spanning \~70 myr from the late Carboniferous to the mid Triassic. The specimens can be categorised into three broad geographic areas associated with the northern, mid, and southern areas of the supercontinent [Pangea](https://en.wikipedia.org/wiki/Pangaea),  corresponding approximately to what would be known today as Eurasia, North America, and South Africa.

> Download the data file `amniotes.nex` and open it in a text editor of your choice. The file can be found on the left-hand panel, under the heading **Data** or in the `data/` folder if you cloned the GitHub repository.

<figure>
	<a id="fig:1"></a>
	<img style="width:100%;" src="figures/0_data_file.png" alt="">
	<figcaption>Figure 1: The nexus file containing the morphological data.</figcaption>
</figure>

Note that the taxon labels contain information about the age of our samples (after the last underscore) and their geographic provenance (after the second underscore).

## Installing the BDMM-Prime and MM packages

The first thing we need to do is install the BDMM-Prime package. This package is not yet listed in the Package Manager in BEAUti, so we need to install it using the package URL.

> - Open **BEAUti** and go to **File > Manage Packages**.
> - Select **Package repositories** at the bottom of this window. This will open the **Package Repository Manager**. 
> - At the bottom click **Add URL** and enter the following address: `https://tgvaughan.github.io/BDMM-Prime/package.xml`
> 
> 	After you click 'OK' your window should look like this: 

<figure>
	<a id="fig:2"></a>
	<img style="width:70%;" src="figures/01-BDMMP-URL.png" alt="">
	<figcaption>Figure 2: The Package Repository Manager window.</figcaption>
</figure>

> - Click **Close**. The BDMM-Prime package should now appear in your list of available packages.
> - Select the BDMM-Prime package and click **Install/Upgrade** at the bottom of the window. This should bring up a message that confirms the package has been successfully installed.

<figure>
	<a id="fig:3"></a>
	<img style="width:100%;" src="figures/01b-BDMMP-install.png" alt="">
	<figcaption>Figure 3: The Package Manager window.</figcaption>
</figure>

> If you haven't done so already, you will also need to install the following two packages:
> 
> - **MM** for models of morphological evolution
> - **BEAST_CLASSIC** for the relaxed lognormal clock model
> 
> These packages should already appear on the same list in the **Package Manager**. You just need to click **Install/Upgrade** for each one.
>
> Restart **BEAUti** to reload packages so you can set up an analysis using **BDMM-Prime**.

----

# Part 1: The constant rate FBD process

## The Partitions panel

Make sure you have downloaded the nexus file associated with this tutorial, `amniotes.nex`. 

> To input your data into BEAUti, either drag and drop the file into the **Partitions** panel and select **Add Morphological Data** and then **OK** or go to **File > Add Morphological Data**.

<figure>
	<a id="fig:4"></a>
	<img style="width:50%;" src="figures/02a-BDMMP-Partitions.png" alt="">
	<figcaption>Figure 4: Options for importing data.</figcaption>
</figure>

This will bring up a window that asks, "Would you like to condition on recording variable characters only (Mkv)?" Since we typically don't collect non-variable morphological characters this option makes sense for our dataset.

> Click **Yes**. 

<figure>
	<a id="fig:5"></a>
	<img style="width:60%;" src="figures/02b-BDMMP-Partitions.png" alt="">
	<figcaption>Figure 5: Options for importing morphological data.</figcaption>
</figure>

Your **Partitions** panel should now look like this:

<figure>
	<a id="fig:6"></a>
	<img style="width:100%;" src="figures/02c-BDMMP-Partitions.png" alt="">
	<figcaption>Figure 6: The Partitions panel.</figcaption>
</figure>

By default the characters are separated into four partitions. This means that characters will be assigned to a substitution rate matrix where the number of possible states corresponds to the maximum observed state for each character. Our dataset contains characters with a maximum observed state number of one, two, three and four (respectively corresponding to characters with two, three, four and five observed states), hence we have four data partitions. The number of characters in each partition is listed under **Sites**.

Note that the **Site Model** is *unlinked* across partitions, while the **Clock Model** and **Tree** are *linked* by default. This means that we will allow each partition to evolve under a separate substitution process, but we will assume that the underlying tree is the same and that the same clock model is shared across partitions. 


> **Topic for discussion**
>
> Would it make any sense to link the site models across partitions here?
>
> <details>
>	<summary>Answer</summary>
> 
> Not really. In this analysis we have four partitions, where each partition has a different number of possible states. If we were to link site models, the site model would need to have the maximum number of states for all the partitions. That means that we would use a model with 5 possible states for each site in our alignment, including sites where we've only observed 2, 3 or 4 states. While it is possible that these states exist and we just haven't observed them, in most cases this doesn't make sense. For instance, if a binary trait stands for presence/absence, allowing for substitutions to 3 further states is nonsensical. 
> 

## The Tip Dates panel

> Navigate to the **Tip Dates** panel and select **Use tip dates**.

Here we can extract information about the age of our samples from the tip labels. For our analysis, the time units are in millions of years, so we'll leave **year** selected in the first drop down menu.

> From the second drop down menu, change **Since some time in the past** to **Before the present**.
> 
> Next, select **Auto-configure**. Leave **use everything** selected and from the drop down menu, select **after last**, leaving the delimiter as **"_"**. This will extract age information from the end of each taxon label.  

<figure>
	<a id="fig:7"></a>
	<img style="width:80%;" src="figures/03_Tip_Dates.png" alt="">
	<figcaption>Figure 7: Options for extracting age information from taxon labels.</figcaption>
</figure>

Note that although our samples span an interval in the past, internally BEAST rescales the tree such that the youngest sample in the tree is at t = 0. The rescaled values are shown under the **Age/Height** column. 

To make sure everything is set up correctly, if you scroll down, the youngest fossil, `Candelaria_barbouri_NAm_239.5`, should have a "0.0" next to it under the **Age/Height** column.

<figure>
	<a id="fig:8"></a>
	<img style="width:100%;" src="figures/03b_Tip_Dates.png" alt="">
	<figcaption>Figure 8: The Tip Dates panel.</figcaption>
</figure>

## The Site Model panel

Under the **Site Model** panel we can specify the model of morphological evolution. For this exercise we'll use the simple Mkv Lewis model for each of our partitions and leave everything as it is.

<figure>
	<a id="fig:9"></a>
	<img style="width:100%;" src="figures/04_Site_model.png" alt="">
	<figcaption>Figure 9: The Site Model panel.</figcaption>
</figure>

## The Clock Model panel
  
> Navigate to the **Clock Model** panel and select **Relaxed Clock Log Normal** from the drop down menu. This will allow rates of evolution to vary across branches in our tree.
 
<figure>
	<a id="fig:10"></a>
	<img style="width:100%;" src="figures/05_Clock_model.png" alt="">
	<figcaption>Figure 10: The Clock Model panel.</figcaption>
</figure>

## The Priors panel

Here we will set up the tree model and all the prior parameters associated with our analysis.

> Navigate to the **Priors** panel.

### Clock model parameters

Let's start with clock model parameters, the overall mean on the clock rate (**ucldMean**) and the standard deviation of the clock rates (**ucldStdev**), which you should already see listed here.

> Expand the options for **ucldMean** by clicking the triangle to the left of this parameter. 
> 
> For the mean on the overall clock rate we will use a **Normal** prior, following the original study, with a **Mean** of **0.001** and a standard deviation (**Sigma**) of **0.01**. Clock rates cannot be negative so the distribution is truncated at zero.

<figure>
	<a id="fig:11"></a>
	<img style="width:100%;" src="figures/06a_Clock_priors.png" alt="">
	<figcaption>Figure 11: The prior on the mean clock rate, ucldMean.</figcaption>
</figure>

> Select the **initial** value option next to the distribution and change **Value** to 0.001. This ensures our analysis starts in a reasonable part of the parameter space. Click **OK**.

<figure>
	<a id="fig:12"></a>
	<img style="width:80%;" src="figures/06b_Clock_priors.png" alt="">
	<figcaption>Figure 12: Setting the initial value for the mean clock rate, ucldMean.</figcaption>
</figure>

> For the standard deviation of the distribution of clock rates, **ucldStdev**, we'll use an **Exponential** prior with a mean of **0.1**. 

<figure>
	<a id="fig:13"></a>
	<img style="width:100%;" src="figures/06c_Clock_priors.png" alt="">
	<figcaption>Figure 13: The prior on the standard deviation of clock rates, ucldStdev.</figcaption>
</figure>

These prior choices match our expectations about rates of morphological evolution estimated in previous analyses of terrestrial vertebrates, i.e., the rates tend to be relatively low, with some degree of variation across branches.

### BDMM-Prime

Next let's set up the phylodynamic model. 

> Select **BDMMPrime** from drop down menu at the top of the **Priors** panel next to **Tree.t:amniotes** and expand the options by clicking on the triangle to the left.  

There are a lot of options for combining parameters using BDMM-Prime, so we need to be careful to select parameter combinations that make sense for our dataset. 

First, we need to choose how we want to parameterize our model - that is, which parameters we want to operate on and add prior information for. Currently, there are three options for parameterising the birth-death model using BDMM-Prime available in BEAUti. For macroevolution we can choose between two options:

- The **Canonical Parameterization**, which is parameterized using the birth ({% eqinline \lambda %}), death ({% eqinline \mu %}), and sampling rates ({% eqinline \psi %}). In this parameterization, and in the context of macroevolution, births usually correspond to speciation events, deaths to extinctions and sampling events to fossilizations.
- The **Fossilized Birth Death Parameterization**, which is parameterized using the net diversification rate ({% eqinline d %}), turnover rate ({% eqinline v %}), and sampling proportion ({% eqinline s %}).

The relationship between the parameterizations is shown in [Table 1](#table:1). Both are useful in macroevolution and your choice might depend on which parameters are of most interest to you or what prior information you have available. In other cases inference under one parameterization might be numerically more stable than another or converge faster. Note that after your analysis is complete, you can always go from one set of parameters to the other via the transformations shown in the table below.


<a id="table:1"></a>

<!-- inline fractions -- MISTAKE IN EQN FOR MU!
| Parameter    | Transformation |
| :-------- | :-------: |
| Speciation ({% eqinline \lambda %})  | {% eqinline \lambda = d / (1 - v) %}    |
| Extinction ({% eqinline \mu %})  | {% eqinline \mu = (vd)(1-v) %}    |
| Sampling ({% eqinline \psi %})  | {% eqinline \psi = (s/ (1-s))((vd)/(1-v)) %}    |
| Net diversification ({% eqinline d %})  | {% eqinline d = \lambda - \mu %}    |
| Turnover ({% eqinline v %}) | {% eqinline v = \mu/\lambda %}     |
| Sampling proportion ({% eqinline s %}) | {% eqinline s = \psi/(\mu + \psi) %}     |
-->

<!-- I prefer fractions, fewer parentheses and easier to read -->

| Parameter    | Transformation |
| :-------- | :-------: |
| Speciation ({% eqinline \lambda %})  | {% eqinline \lambda = \frac{d}{1 - v} %}    |
| Extinction ({% eqinline \mu %})  | {% eqinline \mu = \frac{vd}{1-v} %}    |
| Sampling ({% eqinline \psi %})  | {% eqinline \psi = \left( \frac{s}{1-s} \right) \left(  \frac{vd}{1-v} \right) %}    |
| Net diversification ({% eqinline d %})  | {% eqinline d = \lambda - \mu %}    |
| Turnover ({% eqinline v %}) | {% eqinline v = \frac{\mu}{\lambda} %}     |
| Sampling proportion ({% eqinline s %}) | {% eqinline s = \frac{\psi}{\mu + \psi} %}     |



After you've chosen which parameterization to use, there are still a lot of options left for setting up the model in BDMM-Prime. Later, in Part 2 we will assign taxa to "types" based on their geographic provenance but for the first analysis in this tutorial we'll assume that birth, death, and sampling are all constant through time and across our tree.  

> In this tutorial we'll use the **Canonical Parameterization**, which is the default. Alternatives can be selected from the drop down menu next to **Parameterization**. The canonical option can be applied to scenarios in both epidemiology and macroevolution.
> 
> The only change we'll make here is to the value for **Birth Rate** and **Death Rate**, next to **ALL** and under **Epoch 1**, from **1.0** to **0.1** - these are the initial values for these parameters. Double click on each value to edit it and make sure to press enter. Notice that **Estimate values** is checked for all three of these parameters, which means they will be estimated and not fixed during inference.
> 
> We'll leave **Number of change times** for each of these parameters as **0** meaning these rates are assumed to stay constant over time.  

<figure>
	<a id="fig:14"></a>
	<img style="width:100%;" src="figures/07a_tree-priors.png" alt="">
	<figcaption>Figure 14: Options for the birth, death, and sampling rates.</figcaption>
</figure>

> Scroll down to the next set of parameters of interest. 

**Rho Sampling** is the probability of sampling a lineage at a given time. In macroevolution this is usually associated with the extant sampling probability at t = 0 (the present). In our case study, we have no sampling beyond the period of interest, i.e., the youngest sample included in our dataset is from the Triassic period and is therefore {% eqinline \psi %}-sampled. There are many amniotes that persisted beyond this interval until the present but these are not included in our dataset. This means we have no rho-sampled taxa and it makes sense to leave **Rho = 0.0**. 

The next parameter of note is **Removal Prob** - this is the probability that a lineage is removed from the population upon sampling (through a {% eqinline \psi %}-sampling event) and it makes more sense in an epidemiological context. In macroevolution a removal after sampling implies that a species went extinct after a fossilization event. Since we are convinced this is not the case we need to change the removal probability to 0.0. 

> Select the value listed next to **ALL** and below **Epoch 1** for **Removal Prob** and change it to **0.0**.

Note that we are not estimating the removal probability!

<figure>
	<a id="fig:15"></a>
	<img style="width:100%;" src="figures/07b_tree-priors.png" alt="">
	<figcaption>Figure 15: Options for the rho sampling and removal probability parameters.</figcaption>
</figure>

For now you can leave everything else under this tab as it is. If you close this tab and scroll down you should now be able to see an additional set of parameters listed in the **Priors** panel for the birth, death, sampling rates and the origin time parameter.

For the birth, death, and sampling rate parameters we'll use exponential priors with mean = 0.1. This matches values that are typically estimated for these parameters in paleobiology.

> From the drop down menu next to **birthRateCanonical** select **Exponential** and expand the options. Change the mean of this distribution to **0.1**.

<figure>
	<a id="fig:16"></a>
	<img style="width:100%;" src="figures/07c_tree-priors.png" alt="">
	<figcaption>Figure 16: Prior options for the birth rate parameter.</figcaption>
</figure>

> Go ahead and do the same for the **deathRateCanonical** and **samplingRateCanonical** parameters.

<figure>
	<a id="fig:17"></a>
	<img style="width:100%;" src="figures/07d_tree-priors.png" alt="">
	<figcaption>Figure 17: Prior options for the death rate parameter.</figcaption>
</figure>

<figure>
	<a id="fig:18"></a>
	<img style="width:100%;" src="figures/07e_tree-priors.png" alt="">
	<figcaption>Figure 18: Prior options for the death rate parameter.</figcaption>
</figure>

For the origin time parameter, we'll also use an exponential distribution following the original study, with an offset of 318.1 Ma and a mean of 327.5 Ma. However, recall that BEAST scales the tree such the youngest sample is always zero, so these values will be defined relative to t = 0. 

> Select **Exponential** from the drop down menu next to **originBDMMPrime** and expand the options. Change the **offset** to **81.1**, which is 318.1 minus the youngest age of our sampling interval (237), and change the **Mean** to **10.0**. 

<figure>
	<a id="fig:19"></a>
	<img style="width:80%;" src="figures/07f_tree-priors.png" alt="">
	<figcaption>Figure 19: Prior options for the origin time parameter.</figcaption>
</figure>

> Finally, select the **initial** value box for the origin and change **Value** to **100.0**. The origin time of the process needs to be older than all our fossils, otherwise the process cannot be initialised. Click **OK**.

<figure>
	<a id="fig:20"></a>
	<img style="width:70%;" src="figures/07g_tree-priors.png" alt="">
	<figcaption>Figure 20: Setting the initial value for the origin time parameter.</figcaption>
</figure>

### Adding taxonomic constraints

We're going to use two taxonomic constraints for this analysis. 

> Scroll down to the bottom of the Priors panel and click **+Add Prior**. From the drop down menu select **MRCA prior**. Click **OK**. This will open a box where you can define groups of taxa. 
> 
> For the first constraint, type **'ingroup1'** into the box next to **Taxon set label**. From the list of taxa on the left click on any taxa and use select all (e.g., **Cmd+A** on MacOS or **Ctrl+A** on Windows) to highlight all of them. Scroll down to `Gephyrostegus_bohemicus_Eu_309` and deselect this taxon using **Cmd+click** or **Ctrl+click**. Click the **>>** button. Now all taxa except *Gephyrostegus* should appear on the right. 
>
> Alternatively, you can select all of them, move them to the right and simply move *Gephyrostegus* back to the left using the **<<** button. 

<figure>
	<a id="fig:21"></a>
	<img style="width:60%;" src="figures/08a_tcons.png" alt="">
	<figcaption>Figure 21: Creating the first taxonomic constraint.</figcaption>
</figure>

> Click **OK** and then select the checkbox that says **monophyletic** that appears next to this constraint.

> To add the next constraint, click **+Add Prior** again. From the drop down menu select **MRCA prior**. Click **OK**. 
> 
> For the second constraint, type **'ingroup2'** into the box next to **Taxon set label**. This next constraint will include all taxa except `Gephyrostegus_bohemicus_Eu_309` and `Seymouria_spp_NAm_288.51`.

<figure>
	<a id="fig:22"></a>
	<img style="width:60%;" src="figures/08b_tcons.png" alt="">
	<figcaption>Figure 22: Creating the second taxonomic constraint.</figcaption>
</figure>

> Click **OK** and then select the checkbox that says **monophyletic** that appears next to this constraint.

<figure>
	<a id="fig:23"></a>
	<img style="width:100%;" src="figures/08c_tcons.png" alt="">
	<figcaption>Figure 23: The final set of options for the Priors panel.</figcaption>
</figure>

Use the screenshot above to ensure all your prior distributions and initial values have been set up as described.

## The MCMC panel

> Navigate to the **MCMC** panel and change the **Chain Length** to **1,000,000** (just delete one of the zeros).

<figure>
	<a id="fig:24"></a>
	<img style="width:100%;" src="figures/09_MCMC.png" alt="">
	<figcaption>Figure 24: The MCMC panel.</figcaption>
</figure>

BDMM-Prime allows us to output additional tree files using the **typedTree** and **nodeTypedTree** loggers - these are not useful for our initial analysis and make the analysis more computationally expensive, so we will disable these options for now (but see Part 2 below). 

> Uncheck the boxes next to **Enable Logger** for **typedTreeLogger** and **nodeTypedTreeLogger** after expanding the options for both trees.

<figure>
	<a id="fig:25"></a>
	<img style="width:100%;" src="figures/09b_MCMC.png" alt="">
	<figcaption>Figure 25: Switching off the typed tree and node typed tree loggers.</figcaption>
</figure>

> Go to **File > Save as** to save your xml input file. Call it something like `amniotes_constant_rates_FBD.xml` or anything you like.

> You can leave BEAUti open for the next part of the exercise! However, if you do close BEAUti you can simply go to **File > Load** and select your xml file - all the BDMM-Prime specifications should be reloaded.

----

## Running BEAST

> To run your analysis open the **BEAST** application and navigate to the right working directory. Select your xml file and click **Run**.

This analysis should only take about a minute to run. 

----

## Examining the output

This analysis should have generated the following results files:

- `amniotes_constant_rates_FBD.log` - this file contains all the numerical parameters sampled during MCMC.
- `amniotes.trees` - this file contains the trees sampled during MCMC.

> First open the `.log` file in **Tracer** and explore the output. It should look something like this. 

<figure>
	<a id="fig:26"></a>
	<img style="width:100%;" src="figures/tracer.png" alt="">
	<figcaption>Figure 26: The tracer window for the constant rates FBD model.</figcaption>
</figure>

Of particular interest are the parameters **birthRateSPCanonical**, **deathRateSPCanonical**, and **samplingRateSPCanonical**. The speciation and extinction rates (birth and death) are both ~0.35, while the sampling rate is ~0.01. This suggests that sampling is low relative to the overall diversity of the group. 

Note that this analysis hasn't fully converged but you can download the pre-cooked output for the same analysis that's been run a bit longer.

If you want you can also generate a consensus tree using the standard `.trees` file (`amniotes.trees`) as the input and the approach described in [previous tutorials](https://taming-the-beast.org/tutorials/Total-Evidence-Tutorial/) for trees with sampled ancestors. 

> Open **TreeAnnotator**. For the **Target tree type** stick with the default **Maximum clade credibility tree** and for the **Node Heights** select **Median heights** from the drop down menu. Load `amniotes.trees` under **Input Tree File** and set the **Output file** to something sensible.

<figure>
	<a id="fig:27"></a>
	<img style="width:80%;" src="figures/treeann.png" alt="">
	<figcaption>Figure 27: The TreeAnnotator window.</figcaption>
</figure>

> Open the program [IcyTree](https://icytree.org) in your internet browser. Drag and drop your summary tree file onto the page or go to **File > Load from file...**.

> To generate an image like the one below you can select the following options:
> 
> - **Style > Mark singletons** (to show sampled ancestors)
> - **Style > Internal node text > posterior**
> - **Style > Label precision limit > 2 sig. figures**
> - **Style > Node height error bars > height_95%_HPD**
> - **Style > Axis > Age**
> - **Style > Set axis offset...** and enter 237 in the box, which is end of our sampling window. Click **Ok**.

<figure>
	<a id="fig:28"></a>
	<img style="width:100%;" src="figures/icytree_cbds.png" alt="">
	<figcaption>Figure 28: Summary tree plotted in IcyTree.</figcaption>
</figure>

-------

# Part 2: The multi-type birth-death process

In the second part of this tutorial we will take geographic provenance of the fossils into account and allow the birth, death, and sampling rates to vary between geographic areas. To achieve this we will incorporate a migration rate parameter into the model and in addition, we will allow the migration rate to vary through time, to acknowledge the observation that the interconnectedness of the geographic areas changes considerably over the course of our sampling interval.

> First, create a new folder to run this next analysis.

## The Priors Panel

> Return to the Priors panel in **BEAUti**. Next we're going to extract information about geographic area. Expand the tree model options and scroll down to **Type Trait Set**. Click **Auto-configure**. 
>
> Select **split on character** and from the drop down menu next to **and take group(s):** select 3, leaving the character to split on as **"_"**. Click **OK**.

<figure>
	<a id="fig:29"></a>
	<img style="width:80%;" src="figures/10a_types.png" alt="">
	<figcaption>Figure 29: Options for extracting type information from taxon labels.</figcaption>
</figure>

You should now be able to see the geographic area associated with each fossil in the **Type** column, corresponding to Eurasia (Eu), North America (NAm), and South Africa (SA). 

<figure>
	<a id="fig:30"></a>
	<img style="width:100%;" src="figures/10b_types.png" alt="">
	<figcaption>Figure 30: Type information associated with each sample.</figcaption>
</figure>

> Scroll up and you should see some additional options have been added for the tree model. In particular, options will have been added for the **Migration Rate** and **Birth Rate Among Demes** parameters. 

The migration rate can be interpreted as the rate of change between types (in our case, the migration between geographic areas). We'll estimate this parameter and allow it to vary over time (see below). The birth rate among demes can be interpreted as the rate of "spontaneous" change between types - in epidemiology this might be used together with types that are associated with specific mutations. This parameter doesn't have an analogous meaning in our macroevolutionary context, so we'll simply fix this parameter to zero.

> Double click on the box for **Birth Rate Among Demes** next to **ALL** and enter **0.0**.

<figure>
	<a id="fig:31"></a>
	<img style="width:100%;" src="figures/10c_types.png" alt="">
	<figcaption>Figure 31: The migration rate and birth rate among demes parameters.</figcaption>
</figure>

In this analysis we want to allow birth, death, and sampling to vary across geographic areas. 

> Scroll back up. Note that the initial values for the **Birth Rate** and **Death Rate** may have reverted back to **1.0** after setting the type trait. Change it back to **0.1** for each of the three rates.
>
> You'll see that next to some of the parameters is a checked box called **Scalar values** - this indicates that a single rate applies to all types. Starting with **Birth Rate**, if you uncheck this box, a separate rate will appear next to each type. You can select **Display epoch visualisation** to make sure things are set up correctly.

<figure>
	<a id="fig:32"></a>
	<img style="width:100%;" src="figures/10d_types.png" alt="">
	<figcaption>Figure 32: Type specific birth rates.</figcaption>
</figure>

> Do the same for the **Death Rate** and **Sampling Rate** parameters.

<figure>
	<a id="fig:33"></a>
	<img style="width:100%;" src="figures/10e_types.png" alt="">
	<figcaption>Figure 33: Type specific death and sampling rates.</figcaption>
</figure>

Over the course of our sampling interval, the late Carboniferous to the mid Triassic, we see major changes in continental configuration and the relative connectedness of geographic areas - to quantify the impact this has on migration we can allow the overall migration rate to change through time, assuming piecewise constant variation in the rate.

> Return to the options associated with **Migration Rate**.
>  First double click the the initial value for this parameter and change it to 0.1 and make sure the box next to **Estimate values** is checked. 
>
> Next change **Number of change times:** to 2. Check the box that says **Relative to process length** and click **Distribute evenly** - this means that the time between the origin of the process and the end will be divided into 3 equal-length intervals and each one will be assigned an independent rate parameter. 
>
> Finally, select **Display epoch visualisation** to make sure things are set up correctly.

Note that BDMM-prime sometimes shifts focus to a different parameter after changing a setting. Be careful that you don't accidentally start editing the wrong parameter after one of these jumps! 

> Go through the previous few steps again and check that all settings are as in Figures 31-34.

<figure>
	<a id="fig:34"></a>
	<img style="width:100%;" src="figures/10f_types.png" alt="">
	<figcaption>Figure 34: Time varying mirgration rates.</figcaption>
</figure>

Note that we can specify the probability of each type at the beginning of the process, i.e., at the origin or root. By default these are set to be equal but you can change them in the box next to **Start Type Prior Probs**. This probability can be estimated or you might have strong prior evidence for the root type, but we'll leave this for now.

> Finally, scroll down and assign an **Exponential** prior with a mean of **0.1** to the **migrationRateCanonical** parameter. Each migration rate will be independently drawn from the same exponential prior distribution. 

<figure>
	<a id="fig:35"></a>
	<img style="width:100%;" src="figures/10g_types.png" alt="">
	<figcaption>Figure 35: Prior options for migration rate.</figcaption>
</figure>


## The MCMC panel

> Navigate to the **MCMC** panel. We'll leave all the MCMC chain settings as above, i.e., we'll use a chain length of 1,000,000 steps.

Now that we've added information about types, we can take advantage of some other output options. In particular, BDMM-Prime can use a stochastic mapping approach to generate *edge-typed trees*. These are trees that include information about the phylogenetic history of types. We can generate both *typed trees*, which show where along internal branches type changes (migration events) have occurred or *node typed trees*, which includes type information at internal nodes only.
This can done using the **typedTreeLogger** and  **nodeTypedTreeLogger**, respectively. In this analysis, we're going to use the latter, as shown below, because *node type trees* are more computationally efficient to generate and easier to summarise.

> Expand the options next to **nodeTypedTreeLogger** and check the box next to **Enable logger**. 

<figure>
	<a id="fig:36"></a>
	<img style="width:100%;" src="figures/11b_MCMC.png" alt="">
	<figcaption>Figure 36: Options for the node typed tree logger.</figcaption>
</figure>

> Go to **File > Save as** to save your xml input file in your new folder. Call it something like `amniotes_multi_type_FBD.xml`. 

----

## Running BEAST

> As above, to run your analysis open the **BEAST** application and navigate to the right working directory. Select your xml file and click **Run**.

This analysis will take \~20 minutes to run. Ideally this analysis would be run for much longer, e.g., 10,000,000+ iterations.

----

## Examining the output

This analysis should have generated the following results files:

- `amniotes_multi_type_FBD.log`: this file contains all the numerical parameters sampled during MCMC.
- `amniotes.trees`: this file contains the trees sampled during MCMC.
- `amniotes_multi_type_FBD.typed.node.trees`: this file contains the trees sampled during MCMC with ancestral types mapped onto internal nodes.

> Open the `.log` file in **Tracer** and explore the output. It should look something like this. 
> 
> You might need to expand and adjust the Tracer window so you can see parameter names.

<figure>
	<a id="fig:37"></a>
	<img style="width:100%;" src="figures/12a_tracer.png" alt="">
	<figcaption>Figure 37: The tracer window for the multi-type FBD model.</figcaption>
</figure>

Now we have some additional parameters of interest. In particular, we have three estimates for birth, death, and sampling rates, associated with each type or geographic region. 

> Select the **birthRateSPCanonical**, **deathRateSPCanonical**, and **samplingRateSPCanonical** parameters for all three types simultaneously using the shift key. You should be able to higher relative rates of birth and death associated with North America.

<figure>
	<a id="fig:38"></a>
	<img style="width:100%;" src="figures/12b_tracer.png" alt="">
	<figcaption>Figure 38: The tracer window for the multi-type FBD model showing the birth rates associated with each type.</figcaption>
</figure>

> Scroll down and explore the estimates for the migration rate associated with different intervals. The intervals are labelled from youngest to oldest, **i0**, **i1**, and **i2s**.    

<figure>
	<a id="fig:39"></a>
	<img style="width:100%;" src="figures/12c_tracer.png" alt="">
	<figcaption>Figure 39: The tracer window for the multi-type FBD model showing the migration rates associated with each interval.</figcaption>
</figure>

You should be able to see a larger estimate for migration in the final time bin, which we might expect based on the increase in connectivity between regions in the Triassic. Recall that we defined 3 equal intervals relative to the origin time of the process. The variables appended **_endtime** are the estimated endpoints of each interval, relative to the origin age.

We can summarise information about ancestral types across our posterior distribution of trees using TreeAnnotator. 

> Open **TreeAnnotator** and select `amniotes_multi_type_FBD.typed.node.trees` as your **Input Tree File**.
> 
> For the **Target tree type** stick with the default **Maximum clade credibility tree** and for the **Node Heights** select **Median heights** from the drop down menu. Call your **Output File** something like `amniotes_multi_type_FBD.typed.node.con.tre`. See [Figure 27](#fig:27).

> Open the program [IcyTree](https://icytree.org) in your internet browser. Drag and drop your summary tree file onto the page or go to **File > Load from file...**.

> To generate an image like the one below you can select the following options:
> 
> - **Style > Mark singletons** (to show sampled ancestors)
> - **Style > Colour nodes by > type**
> - **Style > Internal node text > type.prob**
> - **Style > Label precision limit > 2 sig. figures**
> - **Style > Display legend**
> - **Style > Axis > Age**
> - **Style > Set axis offset...** and enter 237 in the box, which is end of our sampling window. Click **Ok**.

<figure>
	<a id="fig:40"></a>
	<img style="width:100%;" src="figures/icytree.png" alt="">
	<figcaption>Figure 40: Summary tree plotted in IcyTree showing the ancestral types.</figcaption>
</figure>

We can use this tree to explore hypotheses about the geographic origins of different lineages. Note that based on node typed trees, it is not possible to make any statements about where and when along a given branch a type change may have occurred. To learn more about options for exploring migration history check out the [BDMM-Prime package website](https://tgvaughan.github.io/BDMM-Prime/).


> **Topic for discussion**
>
> In our analysis we only have one migration rate for each time interval, making three migration rates in total, for 3 geographical areas. What are the implications of this assumption? How can we relax it?  
>
> <details>
>	<summary>Answer</summary>
>
> The implication is that migration between any two locations is equal within a given time interval. In general we may want to relax this, so that we can have different migration rates between different pairs of locations. We may either want symmetric migration rates (3 rates per time interval) or nonsymmetric (6 rates per time interval). 
>
> To allow nonequal migration rates, simply untick the <b>Scalar values</b> checkbox under <b>Migration Rate</b> within the <b>BDMMPrime</b> options in the <b>Priors</b> panel. You can use the <b>Link identical values</b> checkbox to set up a symmetric rate matrix.
> 


----

# Useful Links

- [Bayesian Evolutionary Analysis with BEAST 2](http://www.beast2.org/book.html) {% cite BEAST2book2014 --file BDMM-Prime-for-macroevolution/master-refs.bib %}
- BEAST 2 website and documentation: [http://www.beast2.org/](http://www.beast2.org/)
- [BDMM-Prime package website](https://tgvaughan.github.io/BDMM-Prime/)

----

# Relevant References

{% bibliography --cited --file BDMM-Prime-for-macroevolution/master-refs.bib %}

