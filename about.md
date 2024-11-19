---
layout: page
title: 👨‍💻 About Me
permalink: /about/
---

I am a Vienna-based **Software Engineer** and **Survey Engineer** with over 5 years of professional experience. I hold a degree in **Geodesy** from *Graz University of Technology*, where I contributed as a project and teaching assistant. Currently, I work in the e-commerce and fintech sectors as a **Data Scientist, Engineer, and Analyst** at *Niceshops GmbH* and *Gateway Ventures GmbH*.

Originally from Mrkonjić Grad, Republic of Srpska (BA), I completed high school focusing on advanced studies in **Mathematics, Logic, and Physics**.

---

## 🚀 About this blog:

In this blog, we'll explore a variety of topics. The chart below highlights my personal interests, showing which subjects I’m most passionate about 📊💡:

<canvas id="radialChart" width="400" height="400"></canvas>

---

## 🚀 Technical Skills

<table style="border-collapse: collapse; width: 100%; border-radius: 8px; overflow: hidden; box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);">
<thead style="background-color: #c4c2bb; color: black;">
<tr>
<th style="border: 1px solid #ddd; padding: 12px; text-align: left;">Skill</th>
<th style="border: 1px solid #ddd; padding: 12px; text-align: left;">Proficiency</th>
<th style="border: 1px solid #ddd; padding: 12px; text-align: left;">Skill</th>
<th style="border: 1px solid #ddd; padding: 12px; text-align: left;">Proficiency</th>
</tr>
</thead>
<tbody>
<tr style="background-color: #f9f9f9;">
<td style="border: 1px solid #ddd; padding: 12px;">Python</td>
<td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
<td style="border: 1px solid #ddd; padding: 12px;">PostgreSQL</td>
<td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
</tr>
<tr>
<td style="border: 1px solid #ddd; padding: 12px;">Google Cloud</td>
<td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
<td style="border: 1px solid #ddd; padding: 12px;">MySQL</td>
<td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
</tr>
<tr style="background-color: #f9f9f9;">
<td style="border: 1px solid #ddd; padding: 12px;">C/C++</td>
<td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
<td style="border: 1px solid #ddd; padding: 12px;">JavaScript</td>
<td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
</tr>
<tr>
<td style="border: 1px solid #ddd; padding: 12px;">GIS</td>
<td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
<td style="border: 1px solid #ddd; padding: 12px;">PHP</td>
<td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢</td>
</tr>
<tr style="background-color: #f9f9f9;">
<td style="border: 1px solid #ddd; padding: 12px;">Cloud Computing</td>
<td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
<td style="border: 1px solid #ddd; padding: 12px;">Linux</td>
<td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
</tr>
</tbody>
</table>

---

## 📧 Contact

- **Email**: [milankacar@live.com](mailto:milankacar@live.com)  
- **LinkedIn**: [linkedin.com/in/milan-kacar](https://linkedin.com/in/milan-kacar)  
- **Website**: [milankacar.github.io/CV](https://milankacar.github.io/CV)

---

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
  document.addEventListener("DOMContentLoaded", function () {
    const categoryData = {
      {% for category in site.categories %}
        "{{ category | first }}": {{ category[1].size }},
      {% endfor %}
    };

    const ctx = document.getElementById('radialChart').getContext('2d');
    const categories = Object.keys(categoryData);
    const postCounts = Object.values(categoryData);

    const getChartOptions = (isDarkMode) => ({
      responsive: true,
      plugins: {
        legend: {
          labels: {
            color: isDarkMode ? '#fff' : '#333', // Legend text color
          }
        }
      },
      scales: {
        r: {
          grid: {
            color: isDarkMode ? 'rgba(255, 255, 255, 0.2)' : 'rgba(128, 128, 128, 0.5)',
          },
          angleLines: {
            color: isDarkMode ? 'rgba(255, 255, 255, 0.3)' : 'rgba(128, 128, 128, 0.7)',
          },
          ticks: {
            color: isDarkMode ? '#fff' : '#000',
            backdropColor: 'rgba(255, 255, 255, 0)',
          }
        }
      }
    });

    let radialChart = null;

    const createChart = (isDarkMode) => {
      if (radialChart) radialChart.destroy();
      radialChart = new Chart(ctx, {
        type: 'polarArea',
        data: {
          labels: categories,
          datasets: [{
            label: 'Number of Posts',
            data: postCounts,
            backgroundColor: [
              'rgba(255, 99, 132, 0.2)',
              'rgba(54, 162, 235, 0.2)',
              'rgba(255, 206, 86, 0.2)',
              'rgba(75, 192, 192, 0.2)',
              'rgba(153, 102, 255, 0.2)',
              'rgba(255, 159, 64, 0.2)',
            ],
            borderColor: [
              'rgba(255, 99, 132, 1)',
              'rgba(54, 162, 235, 1)',
              'rgba(255, 206, 86, 1)',
              'rgba(75, 192, 192, 1)',
              'rgba(153, 102, 255, 1)',
              'rgba(255, 159, 64, 1)',
            ],
            borderWidth: 2,
          }]
        },
        options: getChartOptions(isDarkMode),
      });
    };

    // Initial mode detection
    const isDarkMode = document.body.classList.contains('dark-mode');
    createChart(isDarkMode);

    // Listen for dark mode toggle
    const toggleButton = document.getElementById("toggle-mode");
    if (toggleButton) {
      toggleButton.addEventListener("click", () => {
        const isDarkMode = document.body.classList.toggle("dark-mode");
        createChart(isDarkMode);
      });
    }
  });
</script>
