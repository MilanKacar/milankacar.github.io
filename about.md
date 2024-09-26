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
<!-- Add a canvas element to display the radial chart -->
<canvas id="radialChart" width="400" height="400"></canvas>

<!-- JavaScript to generate the radial chart -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script>
  const categoryData = {
    {% for category in site.categories %}
      "{{ category | first }}": {{ category[1].size }},
    {% endfor %}
  };

  const ctx = document.getElementById('radialChart').getContext('2d');
  const categories = Object.keys(categoryData);
  const postCounts = Object.values(categoryData);

  const radialChart = new Chart(ctx, {
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
          'rgba(255, 159, 64, 0.2)'
        ],
        borderColor: [
          'rgba(255, 99, 132, 1)',
          'rgba(54, 162, 235, 1)',
          'rgba(255, 206, 86, 1)',
          'rgba(75, 192, 192, 1)',
          'rgba(153, 102, 255, 1)',
          'rgba(255, 159, 64, 1)'
        ],
        borderWidth: 1
      }]
    },
    options: {
      responsive: true,
      scale: {
        ticks: {
          beginAtZero: true
        }
      }
    }
  });
</script>

At the outset, I expect most topics will revolve around software development. However, as this journey unfolds, I’m excited to see how my interests evolve and where this blog takes us. 🚀

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
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;">GNSS & Signal Processing</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Docker</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;">Java</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Apache Spark</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;">Data Structures & Algos</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Scala</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;">R</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Navigation</td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
  </tbody>
</table>

---

## 💼 Work Experience

### **Data Scientist & Engineer**
*Niceshops GmbH*  
📍 Vienna, Austria  
🗓️ Jan 2021 – Present  
- Financial analysis and cloud computing
- AI & ML tasks: demand forecasting, dynamic pricing
- Course instruction and research development

### **Data Engineer / Team Lead**  
*Gateway Ventures GmbH*  
📍 Vienna, Austria  
🗓️ Aug 2023 – Present  
- Startup valuation models and pitch deck analysis  
- Data mining, lake construction, and management  
- Leading data scraping projects  

### **Software Engineer**  
*TU Graz*  
📍 Graz, Austria  
🗓️ Sep 2019 – Jan 2022  
- Software architecture and data analysis  
- Project management and ML forecasting  
- Focus on electric vehicle testing and automation  

---

## 📚 Education

### **Master's in Computer Science**
*Graz University of Technology*  
📍 Graz, Austria  
🗓️ 2022 – Present  

### **Master's in Geodesy**
*Graz University of Technology*  
📍 Graz, Austria  
🗓️ 2019 – Present  

### **BSc in Geomatics Engineering**
*Graz University of Technology*  
📍 Graz, Austria  
🗓️ 2015 – 2019  

---

## 🎯 Key Projects
- **Scientific Paper Miner**: Competitor geolocation from academic publications.  
- **Pollen Tree Detector**: Automated tree identification using satellite images.  
- **Stock Prices Predictor**: AI and ML-driven market fluctuation analysis.  
- **Various GNSS Signal Processing Projects**: Focused on GNSS signals.  
- **Kalman Filter and Advanced Kalman Filter**: Indoor navigation projects.

---

## 🌍 Languages

<table style="border-collapse: collapse; width: 100%; border-radius: 8px; overflow: hidden; box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);">
  <thead style="background-color: #c4c2bb; color: black;">
    <tr>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">Language</th>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">Proficiency</th>
    </tr>
  </thead>
  <tbody>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;"><strong>Serbian, Croatian, Bosnian</strong></td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;"><strong>English</strong></td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;"><strong>German</strong></td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢🟢🟢🟢</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;"><strong>Russian</strong></td>
      <td style="border: 1px solid #ddd; padding: 12px;">🟢🟢</td>
    </tr>
  </tbody>
</table>

---

## 📧 Contact
- **Email**: [milankacar@live.com](mailto:milankacar@live.com)
- **LinkedIn**: [linkedin.com/in/milan-kacar](https://linkedin.com/in/milan-kacar)
- **Website**: [milankacar.github.io/CV](https://milankacar.github.io/CV)

---

## ⚙️ Hobbies
- **📚 Reading**: Aiming for a book a week  
- **🏋️‍♂️ Fitness**: Passionate about fitness and healthy living  
- **✈️ Traveling**: An avid traveler always looking for new adventures  
- **📈 Trading**: Enthusiastic about market analysis and trading strategies


### Contact me

[milankacar@live.com](mailto:milankacar@live.com)

**📈 Active Users**: <span id="activeUsers"></span>
