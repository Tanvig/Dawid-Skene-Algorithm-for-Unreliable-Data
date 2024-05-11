# Dawid-Skene-Algorithm-for-Unreliable-Data (POS Tagging)

## Introduction
The Dawid-Skene algorithm is a well-known method for estimating class probabilities in categorical data, particularly useful in the context of crowdsourced information where multiple annotators provide labels. The algorithm iteratively updates estimates of the true class labels and annotator reliability through an Expectation-Maximization (EM) approach.

This algorithm was first described in the seminal paper by A. P. Dawid and A. M. Skene, "Maximum Likelihood Estimation of Observer Error-Rates Using the EM Algorithm". You can read more about their methodology here: [Dawid and Skene (1979](https://crowdsourcing-class.org/readings/downloads/ml/EM.pdf).


## How the Dawid-Skene Algorithm Works
The Dawid-Skene algorithm is an Expectation-Maximization (EM) method used to estimate true class labels from noisy categorical data, typically collected from multiple annotators who each classify items into one of several categories. The algorithm not only attempts to determine the most probable true label for each item but also assesses the reliability of each annotator.

### Algorithm Overview
The algorithm iterates through two main steps until convergence:

* **Expectation (E) Step** : Estimate the most probable true labels given the current estimates of annotator reliability.

* **Maximization (M) Step** : Update the estimates of annotator reliability using the current estimates of the true labels.

### Detailed Steps
* **Initialization** :
Initialize the true class probabilities for each item, often based on either random initialization or a simple heuristic like majority voting or averaging the observed labels.

* **E Step** :
For each item, calculate the expected true label probabilities using the current estimates of annotator error rates and the observed labels for that item.
This involves computing a weighted average of the labels, where the weights are determined by the estimated reliability of each annotator and the likelihood of each label given the annotator's error profile.

* **M Step** :
Update the annotator error rates based on the new estimates of the true labels.
For each annotator and each label, estimate how frequently the annotator assigns that label to items belonging to each true class. This step refines our understanding of each annotator's behavior—how likely they are to correctly label items or to make specific types of errors.

* **Convergence Check**:
After each complete cycle of E and M steps, calculate the log-likelihood of the observed data given the model parameters (true class probabilities and annotator error rates).
The algorithm iterates until the change in log-likelihood between successive iterations is below a predetermined threshold, indicating convergence, or until a maximum number of iterations is reached.

### Result Interpretation
* **Class Marginals**: After convergence, the algorithm provides the probabilities for each class being the true label for each item, reflecting the consensus estimate considering all annotators' labels and their estimated reliabilities.

* **Error Rates**: The algorithm also yields error rates for each annotator, offering insights into each annotator's reliability and error patterns.


## About This Repository
This repository contains an implementation of the Dawid-Skene algorithm adapted for part-of-speech (POS) tagging. It uses a dataset where multiple annotators have provided POS tags for a set of words, and includes gold standard labels for evaluation purposes. The implementation runs the Dawid-Skene algorithm to estimate the most likely true POS tags for each word, taking into account potential errors and biases in the annotations.

This implementation was inspired by Dallas Card's Python code for the Dawid-Skene algorithm available on GitHub. His work provides a foundational approach that I adapted for specific use in POS tagging. You can view the original implementation here: [dallascard/dawid_skene](https://github.com/dallascard/dawid_skene/blob/master/dawid_skene.py).

**Note**: The dataset is not uploaded here due to confidential reasons.  


### ipynb file
* Includes statistics of the dataset.
* Includes some visualizations on the dataset.
* Includes the main code to implement the David-Skene algorithm.

### Bried summary of code
* Data Loading and Preprocessing: Reads a CSV file containing words, their corresponding POS tags by different annotators, and the gold standard labels for these words. It formats this data into a structured format suitable for processing.
  
* Count Matrix Creation: Converts the formatted data into a count matrix that records the number of times each tag is assigned to each word by each annotator.

* EM Algorithm Execution: Implements the EM steps (Expectation and Maximization) to iteratively update and refine estimates of the true POS tags for each word and the reliability of each annotator.

* Performance Evaluation: Compares the algorithm’s POS tag predictions against the gold standard labels to calculate accuracy and identify specific instances where the predicted tags differ from the correct tags.
  
* Utility Functions: Includes functions for initializing the algorithm’s parameters, calculating the likelihood of the data under the model, and handling different initializations such as random or based on majority voting.


### David-Skene algorihtm code - Function Descriptions
* **main()** - This is the entry point of the script. It orchestrates the flow of the program by calling functions to load data, create the counts matrix, execute the Dawid-Skene algorithm, and evaluate the algorithm's performance against gold standard labels.

* **read_and_format_data(file_path)** - It loads the dataset from a CSV file and formats it into a structured dictionary for further processing.
  * Inputs: file_path - A string representing the path to the CSV file containing the data.
  * Outputs: A tuple containing two dictionaries:
    * data_dict: Maps each word to a dictionary where the keys are annotators and the values are lists of tags provided by each annotator.
    * gold_labels: Maps each word to its gold standard label.

* **create_counts_matrix(formatted_data)** It transforms the structured data into a count matrix which tallies how many times each tag has been assigned to each word by each annotator.
  * Inputs: formatted_data - A dictionary structured as output by read_and_format_data.
  * Outputs: A tuple containing:
    * words: A list of unique words from the dataset.
    * annotators: A sorted list of annotators who have tagged the words.
    * tags: A sorted list of all tags used.
    * counts: A 3D numpy array where each element at index (i, j, k) indicates how many times the k-th tag was assigned to the i-th word by the j-th annotator.

* **run(responses, tol, max_iter, init)** It executes the Dawid-Skene algorithm to estimate the true class labels and annotator reliability.
  * Inputs:
    * responses: The data structured for EM processing (usually the output from create_counts_matrix).
    * tol: A float that sets the tolerance for convergence of the algorithm.
    * max_iter: An integer setting the maximum number of iterations.
    * init: A string indicating the method used to initialize the algorithm.
  * Outputs: The matrix of estimated true class probabilities for each word.

* **initialize(counts)** - It provides initial estimates for the true class probabilities based on the observed counts.
    * Inputs: counts - The 3D numpy array from create_counts_matrix.
    * Outputs: A 2D numpy array representing initial probabilities of each class for each word.

* **m_step(counts, POS_classes)** - The maximization step in the EM algorithm which recalculates the estimated class probabilities and error rates based on current assignments.
  * Inputs:
    * counts: The counts matrix.
    * POS_classes: Current estimates of class probabilities.
  * Outputs: A tuple containing updated class marginals and error rates matrices.

* **e_step(counts, class_marginals, error_rates)** - The expectation step in the EM algorithm which updates the probabilities of class assignments based on estimated parameters.
  * Inputs:
    * counts: The counts matrix.
    * class_marginals: Current estimates of class probabilities.
    * error_rates: Current estimates of error rates.
  * Outputs: Updated probabilities of class assignments for each word.

* **calc_likelihood(counts, class_marginals, error_rates)** - Calculates the likelihood of the observed data given the model parameters, used to monitor convergence of the algorithm.
  * Inputs:
    * counts: The counts matrix.
    * class_marginals: Class probabilities.
    * error_rates: Error rates.
  * Outputs: The log-likelihood of the data given the model.

*  **evaluate_performance(POS_classes, words, class_indices, gold_labels)** - Evaluates the accuracy of the EM algorithm by comparing the most probable tags assigned to each word against the gold standard labels
   * Inputs:
     * POS_classes: The estimated probabilities of each class for each word.
     * words: The list of words processed.
     * class_indices: A dictionary linking POS tags to their indices.
     * gold_labels: The dictionary of correct labels for each word.
   * Outputs: The accuracy of the predictions and a list of mispredictions for analysis.
 
### Relevant Explanations
* **Initial Class Marginals** - The initial values for the class marginals are set by the relative frequency of each class (wordTag) for each patient (word) across all annotations from all annotators. The initialize() function calculates these values by summing all the annotations for each class assigned to each word and then normalizing these sums so that they form a probability distribution over the classes for each word. This results in each word having an initial probability distribution over all the possible classes it could belong to based on the data provided by annotators.

* **Initial Error Rates** - The initial error rates are not directly set in the initialize() function. Instead, they are first computed during the first M-step of the EM algorithm. Before any EM iteration, there's no direct assignment for error rates; they are effectively assumed based on the initial uniform or naive assumptions about the annotators' performance. Typically, you might start with an assumption that annotators are generally reliable or that each annotator has an equal likelihood of choosing any class, which would then get refined based on the observed data.

### Understanding the algorithm outputs
* **Class Marginals** - This output shows the estimated probability distribution over all possible classes for each item in the dataset. These values represent the algorithm's belief about the most likely true label for each item, considering all the annotations and the estimated reliability of each annotator. Class marginals are used to determine the most likely true class for each item by selecting the class with the highest probability.
  
* **Error Rates** - The error rates are matrices that represent the likelihood of each annotator to assign each possible label to an item, given the item's true class. Each matrix entry (j, l) in the error matrix for annotator k gives the probability that annotator k assigns label l when the true label is j. Error rates are crucial for understanding the behavior and reliability of each annotator. They help identify which annotators are more accurate and which ones might be consistently making certain types of errors.
  
*  **Log-Likelihood** - This is a measure of how well the model fits the data. It calculates the log of the probability of observing the actual annotations given the estimated true class distributions and annotator error rates. Monitoring the log-likelihood during training can help in diagnosing convergence issues. An increasing log-likelihood indicates that the model is improving its fit to the data. The algorithm stops when the increase in log-likelihood between iterations falls below a threshold, indicating convergence.

*  **Convergence Metrics** - Delta-CM (Change in Class Marginals) and Delta-ER (Change in Error Rates): These metrics report the changes in class marginals and error rates between successive iterations of the algorithm. These values are used as a convergence criterion. Small changes suggest that the model parameters have stabilized, and further iterations are unlikely to result in significant improvements.
  
*  **Mispredictions** - When gold labels are available, mispredictions are the instances where the algorithm's most probable class (from class marginals) does not match the gold label. Reviewing mispredictions can be particularly informative for refining the model or understanding specific cases where the algorithm may struggle, which can guide further data annotation or feature engineering.

 
