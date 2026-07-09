# AI job adaptation project README

This project connects data on how exposed each US occupation is to AI with occupation job counts for each metropolitan area in the US. The AI exposure data is from a study called GPTs-are-GPTs ([study](https://openai.com/index/gpts-are-gpts/) | [github](https://github.com/openai/GPTs-are-GPTs)) that was done by OpenAI and the University of Pennsylvania. The metro-level jobs data are from BLS.

[Original code](https://github.com/sfchronicle/sa-AI-job-adaptation) and [story](https://www.expressnews.com/projects/2026/ai-impact-san-antonio-jobs/) are from Wesley Ratko for San Antonio Express-News.

## About the data

In 2024, OpenAI and the University of Pennsylvania [released a study](https://openai.com/index/gpts-are-gpts/) that analyzed AI’s impact on 923 different U.S. occupations. The study looked at all of the tasks that comprise an occupation (as defined by [ONET](https://www.onetonline.org/))  and scored whether each task could be made 50% easier with the assistance of existing AI tools like ChatGPT, or with the assistance of a custom-built tool built that uses AI.

The scoring was as follows:

* 1 point if AI could cut the time in at least half while maintaining quality work
* 0.5 points if AI alone couldn’t accomplish that but additional software likely could
* 0 points if AI couldn’t reduce time or would produce lower quality work

Task scores were then combined to create a score that reflects what share of the job’s tasks are made easier by AI. An AI exposure score of 1 would mean that all job tasks could be made easier. A score of 0 would mean that no job tasks could be made easier. Important note: tasks are also weighted based on whether they are “core” tasks or “supplemental” tasks. “Core” tasks receive higher weight.

### A note about the occupations

The study looks at 923 SOC occupations. Not all occupations scored are present in the [2025 BLS metro-level job counts data](https://www.bls.gov/oes/). Specifically, the study scores detailed occupations like Chief Sustainability Officers (SOC 11-1011.03) but BLS job counts data does not include estimates for occupations with decimal point precision.

Additionally, the BLS jobs data include occupation estimates for which there are no scores. This generally only happens with job titles/codes that are meant to encompass "all other" occupations in an occupation subset. For example, "Educational Instruction and Library Workers, All Other" and "Postsecondary Teachers, All Other" were not scored by the study, presumably because they are too vague to assess.

### A note about the scores

We chose to use the score that gives full weight to tasks that could be made easier by existing AI tools and half weight to tasks that could be made easier but only with additional AI tools built on top of existing AI tools.

There are two other scores that the study researchers calculated:

* one that looks only at the share of tasks that could be made easier with existing AI tools and
* another that gives full weight to tasks that could be made easier with custom tools built on top of existing AI.

If you would prefer to use one of those scores, the code can be easily adapted to accommodate.

Additionally, scores were connected to SOC occupations. BLS releases employment numbers for NEM occupations. We used an official BLS crosswalk to connect AI scores with BLS employment numbers, but some unique BLS codes matched with more than one AI score. In most cases, we chose the match with the best fit. In other cases, we used an average of all matches. You can read more about this in `01_clean_crosswalk.ipynb`.

### A note about the classifiers

The study used both human and AI to classify job tasks. For this analysis, we used only the human classifications.

### A note about “current AI”

The study classifiers used GPT-4 as the “current AI”. OpenAI has since released GPT-5, a newer model with stronger reasoning, coding, writing, research and visual analysis abilities. As AI tools improve, some tasks that once looked only partially exposed may become more exposed over time.

### How are you classifying AI exposure categories?

I’ve chosen to split occupation AI scores into quartiles:

* 0 – 0.25 = low
* 0.26 – 0.5 = medium low
* 0.51 – 0.75 = medium high
* 0.76 – 1 = high

### How are you classifying employment categories?

I’ve also chosen to split the employment values into quartiles. Each location's employment numbers are categorized respective to that location's available occupation employment figures:

* 1st quartile = low
* 2nd = medium low
* 3rd = medium high
* 4th = high



## How to use this repo

If you are a reporter or editor looking to dive deeper into the data for your metro area, you should only need to mess with the `03_xxxx` script for your market. You are welcome to create a new processing script if you need more freedom to explore! If you are trying to use a different AI exposure score than what I used (I used the human_beta) you will also want to adjust `01_clean_crosswalk.ipynb` and `02_merge_bls_scores.ipynb` to adjust the score used and export a new base dataset to use in your analysis script.

What each file does:

- `00_load_bls_data.ipynb` - YOU SHOULD NOT NEED TO RUN THIS FILE AGAIN. This script processes BLS data on employment for different occupations across all US metros, states and nationwide. 
- `01_clean_crosswalk.ipynb` - YOU SHOULD NOT NEED TO RUN THIS FILE AGAIN. This script makes the "best fit" matches between SOC occupations and NEM occupations so we can connect AI scores to BLS employment data.
- `02_merge_bls_scores.ipynb` - YOU SHOULD NOT NEED TO RUN THIS FILE AGAIN. This script merges the BLS employment data with the crosswalked AI study scores. It also calculates an AI exposure category and an employment category columns (high, medium high, medium low and low) and then exports all of the data for use in the analysis files.
- `03_xxxx_analysis_questions.ipynb` - These scripts use the merged BLS employment data+OpenAI study scores to answer market-specific questions such as: Which most common occupations in my metro area are most highly exposed to AI? I have taken a first stab at defining interesting questions to answer.
- `04_format_graphics.ipynb`- YOU SHOULD NOT NEED TO RUN THIS FILE AGAIN. This script takes a market and state and outputs the data necessary to populate both the scatterplot and the nested table available with this project. Once this script is run for your metro area (or state in the case of CT), you will need to manually upload the files it produces to your runsheet tabs `parent_table`and `child_table`. This has already been run for you so unless you changed the AI exposure score variable, you should not need to run this again.

## How to add to this story

To make this a complete story, we suggest you combine the data findings uncovered by this repo with additional reporting and potentially additional datasets.

### People to interview

Your editor will probably talk this through with you, but I would probably suggest finding an expert in your region who can speak to the actual on-the-group impact that AI is likely to have on jobs in your area. The OpenAI study very much tried to avoid saying that AI will eliminate jobs (hence the "AI exposure" phrasing) but the reality may be that in some industries/occupations, making a job easier means that they will hire fewer people to do that job going forward, or may perhaps use layoffs to cut costs now that a single person can do more work using AI. Get a human to respond to some of these concerns.

A lot of the Q&A in the analysis notesbooks are aimed at identifying outlier occupations for your market area. You should use those outlier occupations to try and find workers who are concerned about how AI might transform their job. One market is using this data to ask colleges whether they are seeing fewer kids pursuing certain AI exposed occupations. There are a lot of ways you can use the data to find people.

### **Some interesting things from the research paper**

[Here is a link to the research paper.](https://openai.com/index/gpts-are-gpts/)

“Our analysis indicates that approximately 19% of jobs have at least 50% of their tasks exposed when considering both current model capabilities and anticipated tools built upon them. Human assessments suggest that only 3% of U.S. workers have over half of their tasks exposed to LLMs when considering existing language and code capabilities without additional software or modalities. Accounting for other generative models and complementary technologies, our human estimates indicate that up to 49% of workers could have half or more of their tasks exposed to LLMs.

Our findings consistently show across both human and GPT-4 annotations that most occupations exhibit

some degree of exposure to LLMs, with varying exposure levels across different types of work. Occupations with higher wages generally present with higher exposure, a result contrary to similar evaluations of overall exposure to machine learning (Brynjolfsson et al., 2023). When regressing exposure measures on skillsets using O*NET’s skill rubric, we discover that roles heavily reliant on science and critical thinking skills show a negative correlation with exposure, while programming and writing skills are positively associated with LLM exposure. Following Autor et al. (2022a), we examine barriers to entry by "Job Zones" and find that occupational exposure to LLMs weakly increases with the difficulty of job preparation. In other words, workers facing higher (lower) barriers to entry in their jobs tend to experience more (less) exposure to LLMs”

–

‘We analyze exposure by industry and discover that information processing industries (4-digit NAICS) exhibit high exposure, while manufacturing, agriculture, and mining demonstrate lower exposure. The connection between productivity growth in the past decade and overall LLM exposure appears weak, suggesting a potential optimistic case that future productivity gains from LLMs may not exacerbate possible cost disease effects (Baumol, 2012; Aghion et al., 2018).”

### **Potential additional datasets to include**

One of the mitigating factors in all of this is whether businesses are equipped to enable their employees to use AI, or whether they are equipped to use AI to replace workers.

The Census conducts a survey called the [Business Trends and Outlook Survey](https://www.census.gov/library/stories/2026/05/ai-use-businesses.html) which looks at AI use at businesses of different sizes, geographies and industries. “The survey provides a biweekly, nationally representative view of AI implementation across the business landscape.”

Here are some findings from that survey (many are unsurprising but it’s nice to have some data backup to say these things in a story):

* AI use at US businesses is increasing
* Larger companies are more likely to use AI and they are also increasingly more likely to use AI. In other words, the share of small companies (20 or fewer people) that use AI has remained about the same over the past 6 months while the share of larger companies (250+ people) has increased by about 10 percentage points
* Business sectors with the highest AI usage are Information; Finance and Insurance; Professional, Scientific and Technical Services

If you are interested in metro-level statistics about how many small businesses exist in your metro and what share of total metro jobs those small businesses cover, you can look to [this dataset from the Small Business Administration](https://data.sba.gov/dataset/metropolitan-area-small-business-statistics-2025/resource/ec3528c5-6073-42a6-90f5-ff8bb2e9270a).

Additionally, SF published [this story on AI+job exposure](https://www.sfchronicle.com/california/article/stanford-ai-entry-level-jobs-study-21017046.php) that references a Stanford study that might be of some interest. Seems like the study looked at ADP data to identify occupations + age groups with lower employment counts when compared to pre-AI time periods.

Additionally, OpenAI [continues to produce studies](https://cdn.openai.com/pdf/the-ai-jobs-transition-framework_report.pdf) using the AI job exposure scores that we have used in this piece. This data we are using in our analysis is the underlying data used in this 2026 OpenAI study, but they also add additional metrics. Those metrics may be of interest to your reporting, but I note this primarily because I know 2023 is a while ago (that’s when the OpenAI study we’re using was done), especially when talking about AI, but the research is still being used as valid findings for other studies.
