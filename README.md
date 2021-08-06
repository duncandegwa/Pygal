Pygal is a Python API that enables us to build scalar vector graphic (SVG) graphs and charts in a variety of styles. In this article,you will learn how to make a visualization showing the relative popularity of Python projects on GitHub. We’ll make an interactive bar chart: the height of each bar will represent the number of stars the project has acquired. Clicking a bar will take you to that project’s home on GitHub.

### Prerequisites
You'll need some Python skills to follow along with this tutorial. Furthermore, you must be in a position to work with web APIs in Python.

### Installing pygal
Consider the following command to install pygal:

```python
$ pip install pygal
```

Visit [this](http://www.pygal.org/en/stable/installing.html) if this is your first time using pip.

### The pygal gallery
To see what kind of visualizations are possible with Pygal, visit the gallery of chart types: go to [pygal](http://www.pygal.org/), click Documentation, and then click Chart types. Each example includes source code, so you can see how the visualizations are generated.

### Visualizing repositories
Since we'll want to incorporate more than one repository in our visualisation. Let's create a loop that prints specified information about each of the repositories supplied by the API call so that we can add them all in the visualization:

```python
import requests
URL = 'https://api.github.com/search/repositories?q=language:python&sort=stars' # Make an API call 
r = requests.get(URL)
print("Status code:", r.status_code)
respons_dict = r.json()  # In a variable, save the API response.
print("Total repositories:", respons_dict['total_count'])
# Explore information about the repositories.
repos_dicts = respons_dict['items']
print("Repositories found:", len(repos_dicts))
print("\nSelected info about each repository:")
for repos_dict in repos_dicts:   #loop through all the dictionaries in repo_dicts.
    print('\nName:', repos_dict['name'])
    print('Owner:', repos_dict['owner']['login'])
    print('Stars:', repos_dict['stargazers_count'])
    print('Repository:', repos_dict['html_url'])
    print('Description:', repos_dict['description'])

```

We print the name of each project, its owner, the number of stars it has, its GitHub URL, and the project's description inside the loop:

```bash

Name: public-apis
Owner: public-apis
Stars: 144910
Repository: https://github.com/public-apis/public-apis
Description: A collective list of free APIs

Name: system-design-primer
Owner: donnemartin
Stars: 139818
Repository: https://github.com/donnemartin/system-design-primer
Description: Learn how to design large-scale systems. Prep for the system design interview.  Includes Anki flashcards.
--snip--

Name: Python
Owner: TheAlgorithms
Stars: 113616
Repository: https://github.com/TheAlgorithms/Python
Description: All Algorithms implemented in Python

```

Now that we have some interesting data, let’s make a visualization showing the relative popularity of Python projects on GitHub. We’ll make a bar chart: the height of each bar will represent the number of stars the project has acquired.

```python
import requests
import pygal
from pygal.style import LightColorizedStyle as LCSe, LightenStyle as LSe

# Make an API call and store the response.
URL = 'https://api.github.com/search/repositories?q=language:python&sort=star'
r = requests.get(URL)
print("Status  code:", r.status_code)
# Store API response in a variable.
response_dictionary = r.json()
print("Total repos:", response_dictionary['total_count'])
# Explore information about the repositories.
repos_dicts = response_dictionary['items']
names, stars = [], []
for repos_dict in repos_dicts:
    names.append(repos_dict['name'])
    stars.append(repos_dict['stargazers_count'])
# Make visualization.
mystyle = LSe('#333366', base_style=LCSe)
chart = pygal.Bar(style=mystyle, x_label_rotation=45, show_legend=False)
chart.title = 'Python Projects with the Most Stars on GitHub'
chart.x_labels = names
chart.add('', stars)
chart.render_to_file('py_repos.svg')
```

We start by importing pygal and the Pygal styles we’ll need for the chart. We continue to print the status of the API call response and the total number of repositories found, so we’ll know if there was a problem with the API call. We no longer print information about the specific projects that are returned, because that information will be included in the visualization.

we create two empty lists to store the data we’ll include in the chart. We’ll need the name of each project in order to label the bars, and 
we’ll need the number of stars to determine the height of the bars. In the loop, we append the name of each project and number of stars it has to these lists

Next we define a style using the LightenStyle class (alias LS) and base it on a dark shade of blue. We also pass the base_style argument to use the LightColorizedStyle class (alias LCS). We then use Bar() to make a simple bar chart and pass it my_style.We pass two more style arguments: we set the rotation of the labels along the x-axis to 45 degrees (x_label_rotation=45), and we hide the legend, because we’re plotting only one series on the chart (show_legend=False). We then give the chart a title and set the x_labels attribute to the list names.


Because we don’t need this data series to be labeled, we pass an empty string for the label when we add the data. The resulting chart is shown below:

![Python Projects with the Most Stars on GitHub](https://github.com/duncandegwa/Pygal/blob/main/refining.jpeg)

### Refining Pygal Charts
Let’s refine the styling of our chart. We’ll be making a few different customizations, so first restructure the code slightly by creating a configuration object that contains all of our customizations to pass to Bar():

```python
import requests
import pygal
from pygal.style import LightColorizedStyle as LCSe, LightenStyle as LSe

# Make an API call
url = 'https://api.github.com/search/repositories?q=language:python&sort=star'
r = requests.get(url)
print("Status  code:", r.status_code)
# Store API response in a variable.
response_dictionary = r.json()
print("Total repos:", response_dictionary['total_count'])
# Explore information about the repositories.
repos_dicts = response_dictionary['items']
names, stars = [], []
for repos_dict in repos_dicts:
    names.append(repos_dict['name'])
    stars.append(repos_dict['stargazers_count'])

# Make visualization.
mystyle = LSe('#333366', base_style=LCSe)
myconfig = pygal.Config()   # make an instance of Pygal’s Config class
myconfig.x_label_rotation = 45    # set the  attribute x_label_rotation to 45
myconfig.show_legend = False    #set the attribute show_legend to false
myconfig.title_font_size = 24   #set the font size for the chart’s title
myconfig.label_font_size = 14   #set the font size for the minor label (The minor labels are the project names along the x-axis)   
myconfig.major_label_font_size = 18 #set the font size for the major label(The major labels are just the labels on the y-axis)
myconfig.truncate_label = 15  #use truncate_label to shorten the longer project names to 15 characters
myconfig.show_y_guides = False  #set show_y_guides to False hide the horizontal lines on the graph
myconfig.width = 1000   #set a custom width so the chart will use more of the available space in the browser
chart = pygal.Bar(myconfig, style=mystyle)  #make an instance of Bar, and pass myconfig and style as arguments respectively
chart.title = 'Python Projects with the Most Stars on GitHub'
chart.x_labels = names
chart.add('', stars)
chart.render_to_file('py_repos.svg')

```

The figure below show the restyled chart:
![The styling for the chart has been refined.](C:\Users\ADMIN\Desktop\visualizing-repositories-using-pygal\refining.jpeg)



### Adding Custom Tooltips
In Pygal, hovering the cursor over an individual bar shows the information that the bar represents. This is commonly called a tooltip, and in this case it currently shows the number of stars a project has. Let’s create a custom tooltip to show each project’s description as well.

Let’s look at a short example using the first three projects plotted individually with custom labels passed for each bar. To do this, we’ll pass a list of dictionaries to add() instead of a list of values:

```python
import pygal
from pygal.style import LightColorizedStyle as LCSe, LightenStyle as LSe
mystyle = LSc('#333366', base_style=LCSe)
chart = pygal.Bar(style=mystyle, x_label_rotation=45, show_legend=False)

chart.title = 'Projects in Python '
chart.x_labels = ['public-apis', 'system-design-primer', 'Python']
plot_dictinaries = [
 {'value': 144904, 'label': 'Description of public-apis.'},
 {'value': 139818, 'label': 'Description of system-design-primer.'},
 {'value': 113616, 'label': 'Description of Python.'},
 ]
chart.add('', plot_dictinaries)
chart.render_to_file('bar_desc.svg'
```

We define a list called `plot_dictinaries` that contains three dictionaries: one for the `public-apis` project, one for the `system-design-primer` project, and one for Python. Each dictionary has two keys: `value` and `label`. Pygal uses the number associated with `value` to figure out how tall each bar should be, and it uses the string associated with `label` to create the **tooltip** for each bar. For example, the first dictionary will create a bar representing a project with `144904` stars, and its tooltip will say `Description of public-apis.`

The `add()` method needs a string and a list. When we call `add()`, we pass in the list of dictionaries representing the bars `(plot_dictinaries)`. Pygal includes the number of stars as a default tooltip in addition to the custom tooltip we passed it. 
Figure below shows one of the tooltips:

![Adding Custom Tooltips.](C:\Users\ADMIN\Desktop\visualizing-repositories-using-pygal\Adding_Custom_Tooltips.png)


### Adding Clickable Links to Our Graphs
Pygal also allows you to use each bar in the chart as a link to a website. To add this capability, we just add one line to our code, leveraging the dictionary we’ve set up for each project. We add a new key-value pair to each project’s `plot_dictionary` using the key `xlink`:

```python
import requests
import pygal
from pygal.style import LightColorizedStyle as LCSe, LightenStyle as LSe

# Make an API call and store the response.
URL = 'https://api.github.com/search/repositories?q=language:python&sort=star'
r = requests.get(URL)
print("Status code:", r.status_code)
# Store API response in a variable.
response_dictionary = r.json()
print("Total repositories:", response_dictionary['total_count'])
# Explore information about the repositories.
repository_dicts = response_dictionary['items']

names, plot_dictionaries = [], []
for repository_dict in repository_dicts:
   names.append(repository_dict['name'])
   plot_dictionary = {
   'value': repository_dict['stargazers_count'],
   'label': repository_dict['description'],
   'xlink': repository_dict['html_url'],
   }
plot_dictionaries.append( plot_dictionary)
# Make visualization.
mystyle = LSe('#333366', base_style=LCSe)
chart = pygal.Bar(style=mystyle, x_label_rotation=45, show_legend=False)
chart.title = 'Most-Starred Python Projects on GitHub'
chart.x_labels = names
chart.add('',  plot_dictionaries)
chart.render_to_file('py_rs.svg')
```

Here is the output of the above code:

![Adding Clickable Links to Our Graph.](C:\Users\ADMIN\Desktop\visualizing-repositories-using-pygal\Adding_Clickable_Links_to_Our_Graph.jpeg)

Pygal uses the URL associated with 'xlink' to turn each bar into an active link. You can click any of the bars in the chart, and the GitHub page for that project will automatically open in a new tab in your browser.


### Conclusion
In this tutorial, we have gained an understanding on how to:
- Install Pygal
- Visualize repositories
- Refining Pygal Charts
- Add Custom Tooltips
- Add Clickable Links to Our Graphs

### Further reading
You can learn more about other concepts by [visiting this page.](https://www.pluralsight.com/guides/building-visualizations-with-pygal).

