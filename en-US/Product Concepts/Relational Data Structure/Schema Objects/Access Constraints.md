AC is a relational data structure unique to YashanDB, implemented based on the bounded computation theory's AC model (AC, Access Constraint): by establishing AC on data sources, it realizes a model transformation that reduces large data into smaller forms. During queries, accessing AC data reduces query costs and enhances query speed.

The fundamental idea of bounded computation theory is that given a function f(x), where the parameter x represents a large dataset, most computations do not need to access the entirety of x; only a small subset of x is sufficient to obtain an exact solution for function f(x). The research goal of bounded computation theory is to find that necessary subset of x based on different functions f(x) through semantics.

The AC structure in YashanDB extracts data relevant to the results of f(x) from large datasets according to the function f(x) specified by the user when creating the AC, and stores it in the storage space. The user's request for f(x) shifts from traversing the dataset and performing extensive computations to querying AC and obtaining f(x) with minimal computation.

For example, in an airline dataset containing all historical flight data from all airlines, if a user needs to query the airline with the most flight delays, it would require aggregating a vast amount of data. For this scenario, an AC can be built to extract only the data related to flight delays, while also aggregating duplicates within the data. For instance, city, flight number, and other data may have numerous duplicates, which can be merged. Adding a reference count enables data compression, significantly reducing a massive dataset to accelerate the query.

The roles of AC include:

- Reducing data storage: achieving deduplication or aggregation of data through semantic transformations.

- Reducing data computation: narrowing the scope of computation based on bounded computation and precomputation.

- Defining bounded costs: for data with clear constraint relationships, the computational boundaries can be determined using bounded algorithms to estimate the necessary resources for computation.

- AC achieves query acceleration in a transparent manner for users.

- When source data changes, AC data is dynamically refreshed to maintain data consistency.