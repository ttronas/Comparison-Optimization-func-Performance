# Comparison-Optimization-func-Performance
Finding performant implementations of functions to be optimized.  
A Python notebook that explores different implementations of funcs that are to be optimized by a stochastic optimization algorithm.  

## What is the problem?
When handling large datasets, Data Analysts and Engineers often select Python and especially [**pandas**](https://pandas.pydata.org/) as go-to solution.  
When optimizing problems and writing solutions back in the `pd.DataFrame` in an iterative manner[^1], the intuitive implementation is not always the best choice.  

## What to expect?
This Jupyter Notebook investigates **three different implementations of an optimization function**, which needs to loop through the DataFrame.  
The loops are necessary due to the iterative nature of the dynamic optimization probem: values that are calculated in the previous row are fed into the calculation of the next row.  

**The three implementations are**:  
1. An **"intuitive" approach** looping through the DataFrame and writing into each row separately.
2. An approach **utilizing the `.apply()` method** of DataFrames.
3. A more sophisticated approach using **`numba`** and converting the DataFrame into an **`np.array`**.  

**They all do the same**:  
- using a sample dataset with 100 (obfuscated) measurements of a Waste Water Treatment Plant (WWTP)
- all functions that are being optimized apply a `if-else` condition on a column (by looping through rows)
- optimizing the function is done via the stochastic `scipy.optimize.dual_annealing()` algorithm.
- the results of optimization are returned for comparison
- the optimized parameters are used to be compared to original data (spoiler: the parameters do not work well at all).  

## Results
I observed a huge performance difference: While on the sample set, the performance of `numba` was about 10 times better compared to next best solution.  
![grafik](https://user-images.githubusercontent.com/78816242/168421905-c80a6270-1e32-4fd2-ac4b-40f0786ff598.png)  
`numba` outplays the regular algorithm even further on the full data set (35000 samples): While the regular algorithm takes about 41 min to compute, the already jit-compiled numba function takes only 0.48 s (!).  
![grafik](https://user-images.githubusercontent.com/78816242/168422209-1775b161-2c82-466a-afcd-61bfd1b4af67.png)  
One final remark: When omitting the `@njit` decorator of the function, still the naive Python computation with NumPy arrays took only 34.71 s on the complete dataset.  
This indicates that the pandas package has pretty high access times.

## Lessons learned
- Don't loop through DataFrames (especially writing on the df seems slow).
- A better (and not even a lot more complicated) solution would be to take the DataFrames `.values()` and compute inside a `np.array`
- If you want to make the most out of your Python performance speed while avoiding to switch to compiled languages such as `C`, `Java` or `Julia`, you should definitely try to make your loop compliant to numba's `@njit`.  


## How to use?
Use of the repo should be fairly easy (🤞). If you want to read my main learnings, head over to the [previous chapter](#lessons-learned).  
If you want to figure it out for yourself, download both files (`.csv` and `.ipynb`) into the same directory (or fork the repo to your local machine).  
Then just run the `.ipynb` file on a machine with working Anaconda installation and Jupyter server running. All dependencies should install during first run.  





[^1]: A simple solution such as adding columns with pandas `.shift()` method is only more performant when the shifted values are known beforehand. This isn't always possible (especially in dynamic problems).
