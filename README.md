# Top Products by Profit Across States: Which Adidas products generate the most operating profit across different states in 2021?

## Overview
This web-based visualization tool is designed to enable Adidas's stakeholders — including product managers, sales strategists, and marketing teams — to quickly and effectively understand which products are performing best across different regions of the United States. By presenting this data in an interactive and visually engaging format, the tool aids in strategic decision-making and optimizes product placement according to regional performance.

To achieve our goal, we utilize an interactive US map to distinctly delineate regional boundaries. This geographical segmentation ensures that users can easily identify and select specific states. Following the selection of a state, a pie chart corresponding to that region is displayed. This chart provides an immediate visual breakdown of the operating profit proportions for different product categories within the selected area.

## Visual Elements Description

### US Map Encoding
- **Marks**: Rectangles (blocks)
- **Encodings**: Color for total operating profit, arranged in a grid layout that is roughly geographical to facilitate intuitive understanding of regional distributions.

### Pie Chart Encoding
- **Marks**: Arc segments
- **Encodings**: Colors represent different product categories, and angles represent operating profit percentages, making it easy to perceive which categories are most profitable.

## Choosing Process of Visual Elements

### US Map with Color Coding
- **Rationale**: The grid-based map with blocks representing each state allows for a clear, distinct visualization of geographic data without the complexity of traditional map details. This design makes it easier for users to compare data across states. The color coding based on total sales revenue uses a sequential color scheme to intuitively signify variations in revenue — darker shades represent higher revenue, aligning with natural human perceptions of color intensity.
- **Alternatives Considered**: One alternative might have been using a traditional geographic map with proportional symbols (like bubbles) to represent revenue. However, this was discarded as it could lead to a cluttered map with overlapping symbols, especially in regions with small geographical areas but high data density.

### Hover and Click Interactions
- **Why Tooltip and Pie Charts**: Tooltips on hover provide immediate contextual information without additional clicks, making the data accessible and easily understandable at a glance. We provide quick access to key data points like total sales profit and top products, which enhances user engagement without overwhelming them with information. Moreover, clickable state blocks leading to pie charts allow for deeper exploration of the data, revealing the profit distribution among different product categories within a selected state.
- **Alternatives Considered**: Utilizing bar charts to display the operating profit data by state. While bar charts are excellent for comparing quantities and quickly identifying trends, they present challenges when there is a significant range between the highest and lowest values, which could make it difficult for users to visually grasp subtler variations in data at a glance, especially in states where some product categories might dominate overwhelmingly. Moreover, the bar chart format might not provide as intuitive a breakdown of the proportionate contributions of different product categories to the total profit as pie charts do.

## Overview of Development Process and Challenge
- **Development Process**:
We held three meetings to plan our project. First, we chose our general topic and set project expectations. In the second meeting,
we decided on using the dataset containing sales and operating profit data of Adidas across different US states, and defined the questions we aimed to address through our data visualization.brainstormed on visualization ideas. After brainstorming various visualization options and browsing through examples, we settled on a US map and pie chart to display sales and profit data of addidas products. Each of us worked on parts individually and shared progress on GitHub. In the third meeting, we reviewed our work and assigned tasks for further refinements. 

- **Challenge**: One significant challenge we encountered was accurately positioning the map and pie chart elements to prevent any parts from being cropped or obscured. Additionally, we also faced some challenges when trying to refine the design of the pie chart, particularly in positioning the legend effectively. One team member experienced discrepancies between her local host and the GitHub view, leading to frustration when trying to position elements properly. Furthermore,we also discovered probelms with inconsistent website layout across various devices due to the differing screen sizes and ratios

## Team Roles
Throughout the project, each team member dedicated approximately 20 hours to significantly enhance the interactivity and user experience of our visualization.  Here is our detailed task allocation:
-Junyue Lin: Junyue initialized the GitHub repository for project collaboration, spearheaded the task of importing datasets and crafting the initial visualizations, refined the overall structure, and implemented interactive functionalities such as tooltips and hover details over the pie chart. Additionally, Junyue contributed the integration of the Adidas logo into the project.
- Kristina Wu: Kristina outlined the whole visual elements structures, developed color-coding spreading on the whole map, built interactive functionalities including tooltips and hover details over map, finalized the whole structure, and played a pivotal role in shaping the narrative for our project documentation.
- Cici Xu: Cici revamped the pie chart by refining colors, adding dynamic annotations to display profit percentages, and integrating a comprehensive legend for clarity; contribued refinement ideas to enrich the overall project outcome. 

## Conclusion
Our visualization project “Top Products by Profit Across States” effectively equips Adidas stakeholders with the insights needed to optimize product strategies across the U.S. Through the use of an interactive US map and state-specific pie charts, the tool enables quick and intuitive analysis of operating profits by product category.