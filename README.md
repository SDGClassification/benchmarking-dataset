# Benchmarking Dataset for SDG Classification<!-- omit from toc -->

The SDG Classification Benchmarking Dataset is an open and public resource for evaluating and comparing SDG classification models. It consists of text snippets (2 - 3 sentences), which have been carefully labeled and verified by a team of human experts.

**Note**: The benchmarking dataset currently only covers SDGs 7 and 10. We will be expanding the benchmarking dataset to other SDGs in the coming months.

## Table of Contents<!-- omit from toc -->

- [How to use](#how-to-use)
  - [With Pandas (Python)](#with-pandas-python)
- [Background](#background)
  - [Purpose](#purpose)
  - [Approach](#approach)
  - [Coverage](#coverage)
  - [Limitations](#limitations)
    - [Binary](#binary)
    - [Non-exhaustive](#non-exhaustive)
    - [Ignores sentiment](#ignores-sentiment)
    - [Non-interpretive](#non-interpretive)
- [Model evaluation](#model-evaluation)
  - [Disclaimer](#disclaimer)
  - [Results](#results)
- [Contributing](#contributing)
  - [Join the working group](#join-the-working-group)
  - [Suggestions and feedback](#suggestions-and-feedback)
- [Credits](#credits)
  - [Core contributors](#core-contributors)
  - [List of annotators](#list-of-annotators)
  - [Text snippets](#text-snippets)

## How to use

You can find the benchmarking dataset here: https://github.com/SDGClassification/benchmarking-dataset/blob/main/benchmark.csv

The dataset consists of four columns:

- `id`: Unique identifier for every text (MD5 hash)
- `text`: Text snippet (2 - 3 sentences)
- `sdg`: The SDG that the text is checked against
- `label`: True if `sdg` is present in `text`. False otherwise.

Below is an excerpt of the dataset.

```
     id                                               text  sdg  label
03e9759  Not only does this have potentially negative e...    7  False
04f6c7f  If too much water is stored behind the reservo...    7  False
b87a4f8  Energy efficiency targets are now in place at ...    7   True
12e3f54  Data over the last 30 years suggests that, had...    7   True
135ea60  Large areas of about 500 000 km2 between Mumba...    7  False
```

### With Pandas (Python)

The snippet below shows example code for loading the benchmarking dataset as a Pandas dataframe and comparing the expected and predicted labels.

You can [check out the full Pandas example](https://github.com/SDGClassification/benchmarking-dataset/blob/main/examples/with-pandas.py), which includes code for printing out aggregated statistics for each SDG (such as accuracy, precision, recall, and F1 score).

```python
import pandas as pd

# Load the benchmarking dataset
df = pd.read_csv(
    "https://raw.githubusercontent.com/SDGClassification/benchmarking-dataset/main/benchmark.csv"
)

# Take the text to classify, run it through your model and return the list of
# relevant SDGs in *numeric* format.
# Important: Adapt this method according to your model
def predict_sdgs(text: str) -> list[int]:
    return model.predict(text)


# Classify each text and get the predicted SDGs in *numeric* format
df["predicted_sdgs"] = df["text"].map(predict_sdgs)

# Determine the predicted label by checking whether the predicted SDGs contain
# the SDG from the benchmarking dataset. Predicted label is set to `True` if
# the benchmark's SDG is contained in the predictions.
df["predicted_label"] = df.apply(lambda row: row.sdg in row.predicted_sdgs, axis=1)

# Compare expected and predicted labels
df["is_correct"] = df["label"] == df["predicted_label"]
```

## Background

### Purpose

There exist a number of different models and approaches for classifying texts with respect to the Sustainable Development Goals (SDGs). Refer to [this list](https://globalgoals-directory.notion.site/PUBLIC-List-of-AI-driven-SDG-Classification-Initiatives-fe4770a173194e0b980f95b330a8a83b) for a (non-exhaustive) overview of different SDG classifiers and initiatives.

To further advance the field of SDG classification, it is important that we understand the strengths and weaknesses of the available tools.

This public and open benchmarking dataset allows us to evaluate and compare the accuracy of available SDG classifiers, so that we can better grasp their capabilities and limitations.

Our hope is that this benchmarking dataset can contribute to us building more accurate, more reliable and more trustworthy models and approaches in the future.

### Approach

This benchmarking dataset has been carefully curated by a [working group](#core-contributors) with expert knowledge in the domain of the SDGs. Unlike other datasets, this benchmarking dataset has been fully annotated and verified by humans.

To ensure a robust benchmarking dataset, the texts for each SDG were independently annotated by several human experts. For most texts, the annotations made by these experts were identical. For some texts, however, there initially were disagreements between the annotations. These disagreements were resolved through discussion between the experts until consensus was reached.

In some cases, consensus was reached by making slight modifications to a text in order to remove ambiguity.

### Coverage

The dataset currently covers SDGs 7 and 10.

| SDG                                | Number of texts | Texts with SDG | Texts without SDG |
| ---------------------------------- | --------------: | -------------: | ----------------: |
| SDG 7: Affordable and clean energy |             100 |             50 |                50 |
| SDG 10: Reduced inequalities       |              61 |             32 |                29 |

Note that the number of texts across the SDGs as well as the number of texts with and without SDG are currently not balanced. The working group is focusing first on expanding the benchmark to all SDGs with at least 50 texts each &mdash; the exact number of texts for each SDG will depend on how difficult it is to reach consensus within the working group. In the long-term, the group is planning to expand the number of texts for each SDG to one hundred.

### Limitations

While we have invested a lot of time and care to ensure that this benchmarking dataset is as robust as possible, there are important limitations to be aware of when using this dataset to evaluate your model.

This benchmark serves as a starting point for evaluating the accuracy of a model. It is important to remember that models may have been developed for domain-specific use cases, languages, and/or other specializations that cannot be comprehensively evaluated with this benchmarking dataset.

#### Binary

We evaluated each text only with respect to whether it directly addresses a given SDG and its targets. This assessment was binary (`true` vs `false`), which greatly reduced the complexity.

We did not evaluate how strong the reference is, we did not evaluate whether other SDGs are present in the text and we did not evaluate what the primary SDG in each text is.

#### Non-exhaustive

We have tried to include a variety of texts in this benchmarking dataset in order to cover the breadth of each SDG. For example, for SDG 7, we have included texts related not just to renewable energy, but also texts related to energy access, affordable energy, energy efficiency, etc...

That said, due to the wide spectrum of issues covered by the SDGs, we cannot guarantee that this benchmarking dataset provides exhaustive coverage of all the relevant issues for each of the SDGs.

#### Ignores sentiment

For the sake of simplicity, we ignored valence and sentiment in the texts. A text was classified as addressing a given SDG, even if the content was discussed in a negative light. For example, "We will reduce renewable energy subsidies" would still be classified as `true` for SDG 7.

#### Non-interpretive

Texts were only assigned to a given SDG, if the text directly addressed that SDG and/or its targets. "We are subsidizing the costs of silicon" would not qualify for SDG 7, even though silicon is one of the materials for making photovoltaic cells and reduced cost of silicon might have indirect benefits for the expansion of solar energy.

We ignored indirect relevance in texts because correct assessments would require enormous thematic expertise, and even then such interpretations would often remain highly subjective and controversial.

## Model evaluation

### Disclaimer

Several models have been tested against this benchmark. The results can be used as one criterion when evaluating models. Importantly, there are a range of other important aspects to consider that are not tested by this benchmark, such as cost, speed, and coverage of SDG targets. In addition, many models have been developed and optimized for domain-specific use cases (e.g. analyzing policy documents, research articles or websites) and their performance on this benchmark may not be representative of their performance on their domain-specific use case.

### Results

The table below shows the accuracy (in percent) of models tested against this benchmark. Clicking on one of the models provides additional details, including metadata about the model, F1 score, precision and recall.

| Model                                                         | All | SDG 7 | SDG 10 |
| ------------------------------------------------------------- | --: | ----: | -----: |
| [AFD SDG Prospector](evaluations/sdg-prospector/)             |  92 |    95 |     87 |
| [Aurora SDG](evaluations/aurora-sdg/)                         |  80 |    89 |     66 |
| [Global Goals Directory](evaluations/global-goals-directory/) |  87 |    91 |     80 |
| [JRC SDG Mapper](evaluations/sdg-mapper/)                     |  80 |    86 |     70 |

More models will be added in the future.

Have you benchmarked a model that is not yet in our list? Please open an issue and share the results with us, so that we can add the model to the table above.

## Contributing

### Join the working group

The [core contributors](#core-contributors) meet regularly to improve and expand this benchmarking dataset. If you want to join us, please reach out to [finn@globalgoals.directory](mailto:finn@globalgoals.directory).

### Suggestions and feedback

This benchmarking dataset is a living document and we will continue to make adjustments and improvements based on feedback. Please open an issue to share your ideas, suggestions, and feedback.

## Credits

### Core contributors

The benchmarking dataset has been created by the Benchmarking Working Group of the [SDG Classification Expert Group](https://sdg-ai.org/). Core contributors are Finn Woelm, Meike Morren, Steve Borchardt, Jean-Baptiste Jacouton and Gib Ravivanpong.

### List of annotators

We especially thank our annotators for making this project possible.

- **SDG 7**: Steve Borchardt, Jean-Baptiste Jacouton, Gib Ravivanpong, Finn Woelm
- **SDG 10**: Steve Borchardt, Jean-Baptiste Jacouton, Meike Morren, Gib Ravivanpong, Finn Woelm

### Text snippets

We used the fantastic [OSDG Community Dataset](https://zenodo.org/doi/10.5281/zenodo.5550237) for selecting texts to include in our benchmarking dataset. The OSDG Community Dataset has been created by 1,400+ volunteers and is available under a CC-BY 4.0 license.

Citation: OSDG, UNDP IICPSD SDG AI Lab, & PPMI. (2024). OSDG Community Dataset (OSDG-CD) (Version 2024.01) [Data set]. Zenodo. https://doi.org/10.5281/zenodo.10579179
