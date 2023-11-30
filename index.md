---
layout: default
---

# The team
* Dr. Malcolm MacIver - Principal Investigator
* Dr. Daniel Dombeck - Principal Investigator
* Dr. German Espinosa - Posdoctoral Researcher
* Dr. Chris Angeloni - Posdoctoral Researcher
* Alexander Lai - Ph.D. Candidate
* Gabrielle Wink - Ph.D. Candidate
* Lily Browdy - Undergrad Student
* Judith Murciano - Undergrad Student
* Benjamin Zitzewitz - Undergrad Student
* Parker Ryan - Undergrad Student
* Joe Reed - Undergrad Student
* Joshua Chi - Undergrad Student

<!---
# Public datasets
1. [Mus Musculus in mid entropy environment (21_05)](https://www.google.com)
2. [Mus Musculus and Robot (aversive) in mid entropy environment (21_05)](https://www.google.com)
3. [Mus Musculus in open environment (00_00)](https://www.google.com)
4. [Peromyscus Maniculatus in mid entropy environment (21_05)](https://www.google.com)
5. [Peromyscus Maniculatus and Robot (aversive) in mid entropy environment (21_05)](https://www.google.com)
6. [Peromyscus Maniculatus environment (00_00)](https://www.google.com)
-->

# The cellworld Library
Use the cellworld library to generate new world configurations or open existing ones, explore experimental results, plot results and more.

## Prerequisites
Python 3.6 or newer.

## Installation
```shell
>> pip pinstall cellworld
```

## Examples

### Opening a world from the library
code:
```python
import cellworld as cw
import matplotlib.plyplot as plt
world = cw.World.get_from_parameters_names("hexagonal", "canonical", "21_05")
display = cw.Display(world)
plt.show()
```
result:
![world21_05](assets/img/21_05.png)

# Presentations of science done with cellworld
## Presentation on Nov 1 2023 to the Northwestern Institute on Complex Systems

<div style="text-align:center; transform: scale(1.5);">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/your-video-code" frameborder="0" allowfullscreen></iframe>
</div>
