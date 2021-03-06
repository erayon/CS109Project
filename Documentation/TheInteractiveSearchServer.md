
The requirements from the search service were clear. The input is the dataset and the user actions. The output should be two lists: one list with the **most relevant terms** (based on the number of terms we would like to show in the user interface.) The second list shall have the **most relevant clinical trials**. Even though it's not clear immediately what are "the most relevant" terms and clinical trials, some decisions were easy:

(1) When a user selects a term, the documents that do not contain this term are not relevant

(2) When a user does not select any term from the list of term that is presented, then all the terms in the list become irrelevant.

Next, we defined the requirements from algorithm that will select the terms to present to the user (from the collection of all terms that the user did not reject.)

The purpose of the algorithm is to build a list of "Most Relevant Terms" (MRT). The list is then presented to the user through the user interface and the user can select the most representative term to the query.

We defined two main properties of "Most Relevant Terms" (MRT) that the algorithm shall try to balance:

(1) Relevancy - Include the most frequent terms so the user will review the terms that are the most relevant to the dataset

(2) Coverage - Maximize the number of documents that include the some of the terms so the user will review as much documents as possible from the dataset

The algorithm constructs the list of N terms by interactively:

(1) Calculate term frequencies

(2) Add k most frequent terms to the returned list of terms. So the parameter k controls the Relevancy of the list of terms. If k is higher than the Relevancy of the list of terms is also higher.

(3) Remove all documents that include at least m from the k terms that were selected in the previous step from the list of documents used to calculate frequencies for the next iterations. So the parameter m controls the Coverage of the list of terms selected by the algorithm because it enables documents that do not contain many frequent terms, to influence more the terms-frequency calculation of the next iteration. If m is lower then the Coverage of the list of terms selected by the algorithm is higher.

After selecting the list of terms, the next step was to define what are the "most relevant" clinical trials that the service shall serve. We decided that documents should be relevant not only to the terms that the user already selected, but also to the list of terms that is presented to the user. We have used **tf-idf vectorization with cosine similarities** between the list of the terms that the user already selected together with the list of terms presented to the user and each of the clinical trials that were not removed from the context (remember that trials that do not contain a term that the user had already selected are removed from the context.)

After implementing the service, we have performed **Monte Carlo simulation**. In the simulation we modeled interactive search sessions and measured the performance of the service with **different sets of (k,m) parameters**. The score of each set of parameters was the tf-idf based cosine distance between the list of parameters that the user has selected in each simulation to the list of documents that has been presented to the user at the end of each simulation. At the end of the simulation we scattered the results on a (k,m)  grid and used differential point size, so the size of the points will correlate to the score of each simulation. Using the results we set the optimal values for the parameters k and m.


/Images/TermSelectionAlgorithmOptimization.png
