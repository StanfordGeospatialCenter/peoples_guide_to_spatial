# Cartographic Design with QGIS: Creating Maps for Academic Journals

**📖 For the full workshop with images and interactive examples, see the [original Stanford GIS Cartography workshop](https://sites.google.com/stanford.edu/gis-cartography/workshops/maps-for-academic-journals).**

## Overview

Maps, graphs, and other figures are essential elements of academic publication. While the narrative text carries content, maps and illustrations are often where readers' eyes stop as they scan through articles. They represent opportunities for dense visual communication in limited space.

Academic publication presents unique challenges to creating effective maps. Journals impose strict constraints on figure size, resolution, color options (often grayscale only), and format specifications. Learning to work within these constraints requires a different approach than creating larger presentation or web maps.

**Developed in cooperation with Dr. Michelle Tobias** (GIS Data Specialist at the UC Davis DataLab). You can view [Michelle's version of this workshop on GitHub](https://github.com/MicheleTobias/Workshop-Cartography-Journal-Figures).

## Learning Objectives

By the end of this lesson, you will:
- Understand the unique design challenges of journal publication maps
- Learn best practices for visual hierarchy, typography, color, and projections
- Create a publication-ready map within journal specifications
- Apply QGIS styling and layout tools for print media
- Export maps in formats acceptable to academic publishers

## Prerequisites

This lesson assumes you already have:
- Basic understanding of common spatial data formats (vector and raster)
- Familiarity with desktop GIS software and QGIS specifically
- (See our [Intro to QGIS workshop](../week00/06_introducing_qgis_with_basic_cartographic_design.md) if you need a refresher)

## Common Design Issues for Journal Publication

The most common design problems encountered when publishing maps in journals are:

- **Small map sizes** – Limited to 1 or 2 column widths on a page
- **Limited color** – Often restricted to grayscale, or charged per color figure
- **Low resolution** – Print constraints and file size limits
- **Data citation and licensing** – Required in captions, impacts map design

---

# Steps for Creating Journal Map Figures

## 1. What Map Are You Making?

The first step in making any map is understanding the **message** you are trying to convey and identifying **your audience**. Your map should be designed specifically for the topic and the people who will see it.

### Real-World Examples

**Swift Boat Route Map** (full color, book publication): Detailed color and context draw attention to the path while maintaining geographic context for readers unfamiliar with the Vietnam War region.

**Giant Panda Habitat Map** (grayscale, journal publication): Print limitations meant no color and minimal design space. However, knowing the audience was familiar with the subject, designers chose to focus on core provinces and panda habitat while removing background details.

*[See the [original workshop](https://sites.google.com/stanford.edu/gis-cartography/workshops/maps-for-academic-journals) for visual examples of both maps.]*

### Key Questions to Ask:
- What is the primary story or data I'm visualizing?
- Who is my audience and what geographic knowledge do they have?
- What level of detail is necessary to communicate my message?

---

## 2. Data Selection

You will need geospatial data to build your map, but be careful not to use raw analysis data for publication figures. **Raw GIS data is often too dense or detailed for small figures.** What looks acceptable on a desktop becomes an illegible mess when printed at 3 inches wide.

### Data Scale Matching
Match your data scale to your map scale—don't use overly detailed or overly simplified data for the final output size. A good resource for general-purpose data at multiple scales is [Natural Earth Data](https://www.naturalearthdata.com/downloads/), which provides data at three levels of detail:
- 10m scale (global, less detail)
- 50m scale (regional)
- 110m scale (continental overview)

---

## 3. Journal Art Specifications

**Before you start creating the map, understand the journal's art specifications.** Every journal, book, and magazine has specific requirements for image submissions. Failure to meet these guidelines risks your map being excluded or modified by the publisher.

### Example: Nature Journals

Nature's formatting guide specifies:

**Size:**
- 89 mm (1 column) or 183 mm (2 column) wide, max 247 mm high
- Figures should match one of these width dimensions exactly

**Color:**
- Four-color reproduction (CMYK: cyan, magenta, yellow, black)
- Color figures incur publication charges—consider grayscale

**Font:**
- No specific requirements for maps; use common, readable fonts
- Some journals require open-source fonts

**Format & Quality:**
- Electronic format (.jpg for review; .eps, .tiff, or .pdf for publication)
- Typically 300 dpi for small print; 600 dpi for large or detailed maps
- Specify color space (CMYK preferred for print, RGB acceptable)

**Other Limitations:**
- No separate panels unless they relate to the main figure
- One map per figure unless components are directly related

### Finding Your Journal's Specs

Publisher specs are often scattered across the author instructions. Look in:
- Figure specification sections
- Photography or graphics guidelines
- General submission guidelines

Be persistent—there's no point starting a map without these details.

---

## 4. Page Setup in QGIS

Once you know your journal specifications, set up your QGIS page dimensions **before** styling data.

1. Open QGIS with your data layers
2. Go to **Project → New Print Layout**
3. Right-click the page → **Page Properties**
4. Set **Size** to Custom
5. Enter **Width** and **Height** from your journal specs (e.g., 183 mm × 183 mm)
6. Save the project

**Don't leave this step for the end**—building a map at default letter size (8.5" × 11") and shrinking it later will force compromises on readability and design.

---

## 5. Design Principles for Journal Maps

### Visual Hierarchy

Visual hierarchy separates images into **background (ground)** and **foreground (figure)** elements. The order of map features in the visual hierarchy indicates their relative importance.

- **Ground elements** are contextual (like coastlines or country boundaries)
- **Figure elements** represent your data (what you're analyzing)
- Anything that stands out visually will move higher in the hierarchy

**How to create hierarchy:**
- Use size, color, and contrast to emphasize important features
- Push less important elements to the background with lighter colors or thinner lines
- Group similar features; separate dissimilar ones

### Color and Grayscale

**For grayscale publication:**

Even without color, you can create strong visual hierarchy using different shades of gray:
- **10%–90% black** reads as "ground" (background)
- **Full black (100%)** reads as "figure" (data)
- **Full white (0%)** often reads as "no data" or empty space

The greater the percent-black difference between features, the greater the visual contrast.

**Example: Giant Panda Habitat Map**
- Light gray background = China's general geography
- Medium gray = historic panda range
- Full black = current panda range (most important)

*[See the [original workshop](https://sites.google.com/stanford.edu/gis-cartography/workshops/maps-for-academic-journals) for this grayscale hierarchy example.]*

**For color publication:**

When color is available, use it strategically:
- Keep the basemap subdued (light grays)
- Use saturated color only for your data
- Use natural color associations (blue for water, greens for vegetation)
- **Avoid rainbow color ramps** for sequential or divergent data—they can misrepresent values and are problematic for colorblind readers

**Better alternatives:**
- Single color going light → dark (sequential data)
- Two-color divergent scheme (data diverging from a center value)

*[See the [original workshop](https://sites.google.com/stanford.edu/gis-cartography/workshops/maps-for-academic-journals) for visual examples of color ramp alternatives and the "rainbows are for unicorns" example.]*

### Fonts

Keep fonts simple and readable:
- Follow journal specifications
- Stick with common fonts (Helvetica, Arial, Times New Roman) unless the journal specifies otherwise
- Avoid decorative fonts or using more than 2 fonts per map
- Use font style variations (bold, italic, size, color) to link features to the visual hierarchy

### Projections

All maps are distorted to some degree. Your choice of coordinate system affects distortion type and map appearance:

- **Never use geographic (unprojected) coordinates**—always project your data
- **Avoid Mercator projections**—they severely distort area and shape in mid-to-upper latitudes. Knowledgeable readers may perceive this as unfamiliarity with best practices
- **For density maps** (people per square mile), use equal-area projections so regions are comparable
- **For general-purpose or study-area maps**, conformal projections (which preserve shape) are more familiar and best

### Basemaps

Built-in basemap services (in QGIS or ArcGIS) are usually a poor choice for journal figures because they:
- Are typically full color and fairly detailed
- Compete for visual attention with your data
- Flatten the visual hierarchy
- Make it harder for readers to focus on your message

**Better approach:** Create a custom, minimal basemap using light gray lines and labels. [Learn how on YouTube](https://youtu.be/lMfL9VsHcWA).

*[See the [original workshop](https://sites.google.com/stanford.edu/gis-cartography/workshops/maps-for-academic-journals) for visual comparisons of default basemaps vs. custom minimal basemaps.]*

---

## 6. Layout: Marginalia and Arrangement

### Titles

Map figures rarely have proper titles. Instead, use the **figure caption** to title and briefly describe the map.

### North Arrows and Scale Bars

- Maps of recognizable regions don't need north arrows (north assumed at page top)
- **Include a north arrow only if the map is rotated**
- Scale bars are useful for unfamiliar regions or to clarify actual distances

### Legends

Legends are only needed when symbols aren't self-evident. Whenever possible:
- **Use direct labeling instead of legends**
- Don't create a legend entry for something you can label directly on the map
- Remove unnecessary legend entries

### Figure Captions

Captions explain why the figure is in the article and what readers need to know. A well-written caption should include:

- **Figure number and title:** What the map shows
- **Description:** The story or data being visualized
- **Data citation:** In the journal's preferred style, including source and cartographer
- **Contextual information:** Anything needed to interpret the map

**Example:**
> Figure 1: Study site locations. Black squares indicate sites treated with experimental weed killer and open circles indicate control sites. Data from Smith et al. (2021).

### Alignment and Frames

Carefully place all layout elements (legends, scale bars, labels, etc.):
- Don't obscure the main map or data
- Use uniform spacing between items and map edges
- **Avoid adding extra frames**—each new frame creates an obstacle to visual flow

The visual phenomenon "1+1=3" shows that gaps between frames create the illusion of an additional line. Removing unnecessary frames declutters and improves the figure.

---

## 7. Exports

Refer to the publisher's art specifications and follow their recommendations. Most will ask for raster exports (.jpg, .tiff, or .png); occasionally you'll need vector formats (.ai, .eps).

**If you don't have Adobe Illustrator:**
- Substitute .svg or .pdf (both editable in Illustrator)

**Important consideration:**
- **Vector maps** can be edited by the publisher (lack of control)
- **Raster maps** cannot be easily modified (more control)

---

# Hands-On Exercise: Kīlauea 2018 Eruption Map

## Premise

On May 3, 2018, a new eruption began along the East Rift zone of Kīlauea volcano in Hawaii. The eruption:
- Forced evacuation of 2,000 residents
- Covered 13.7 square miles with fresh lava
- Added 875 acres of new land to the island
- Became the most destructive U.S. volcanic event since Mount St. Helens (1980)

**Your task:** Create a map overlaying the 2018 main lava flow with historic eruptions (1790, 1840, 1955) for a fictional Nature journal article.

Learn more: [2018 Lower Puna Eruption (Wikipedia)](https://en.wikipedia.org/wiki/2018_lower_Puna_eruption)

## Download Data

[Download the data folder](https://drive.google.com/file/d/1O59PW_6YVcrNjhbQ-7Jr9u-w3rMpOKU6/view?usp=sharing) and extract to your Desktop.

### Data Contents:

**HI_base_data/**
- Hawaii state outlines (2 scales)
- Water effect buffer layer

**contact_polygons/**
- Lava flow contact polygons for 1790, 1840, 1955 eruptions
- Fissure line layer for East Rift area

**KIL_2018_raster/**
- Composite thermal IR (Band 10) from Landsat 8
- Combines images from 5/16/2018–6/17/2018

**KIL_2018_poly/**
- Single vector polygon of 2018 East Rift eruption

## Art Specifications to Follow

Use the Nature journal specifications from earlier in this lesson:
- 183 mm width (2 column), 247 mm max height
- .jpg export
- CMYK color space (RGB acceptable for conversion)
- 300 dpi

---

## Step 1: Set Up the Map Project

1. Open QGIS and create a new project
2. In the **Browser** panel, create a new connection to your data folder on Desktop
3. Add these layers to your project:
   - coast_UTM5
   - 1790_contacts
   - 1840_contacts
   - 1955_contacts
   - Kilauea_fissures
4. Save the project with a descriptive name (e.g., "Kilauea_2018_Journal")

*[See the [original workshop](https://sites.google.com/stanford.edu/gis-cartography/workshops/maps-for-academic-journals) for detailed screenshots of each step.]*

---

## Step 2: Set Up the Print Layout

1. **Project → New Print Layout** and name it "Nature Specs"
2. Right-click the white page → **Page Properties**
3. Set **Size** to Custom
   - Width: 183 mm
   - Height: 183 mm
4. Click **Save**

Close this window; we'll return after styling data.

---

## Step 3: Style the Data Layers

Open the **Layer Styling Panel** for live updates:
1. **View → Panels → Layer Styling**
2. Check **Live update** to see changes in real time
3. Right-click a contact layer → **Zoom to Layer** (adjust your view)

### Base Layer (coast_UTM5)

1. Select coast_UTM5 in the Layers panel
2. In Layer Styling, select **Simple Fill**
3. **Fill color:** Set H=0, S=0, V=85% (light gray)
4. **Stroke color:** Set H=0, S=0, V=50% (medium gray)
5. **Stroke width:** Leave at 0.26 mm

*[See the [original workshop](https://sites.google.com/stanford.edu/gis-cartography/workshops/maps-for-academic-journals) for screenshots of the color picker interface.]*

### Lava Flow Contacts (Create Visual Hierarchy)

Set fill colors for each contact layer (strokes transparent):

**1790_contacts:**
- Fill: H=0, S=0, V=75%
- Stroke: 0% Opacity (transparent)

**1840_contacts:**
- Fill: H=0, S=0, V=65%
- Stroke: 0% Opacity (transparent)

**1955_contacts:**
- Fill: H=0, S=0, V=50%
- Stroke: 0% Opacity (transparent)

This creates a grayscale hierarchy—older eruptions appear lighter, newer darker.

*[See the [original workshop](https://sites.google.com/stanford.edu/gis-cartography/workshops/maps-for-academic-journals) for screenshots showing the styled contact layers.]*

### Fissures (Kilauea_fissures)

Use full black to emphasize these small features:

1. Select Kilauea_fissures → **Simple Line** in styling panel
2. **Color:** H=0, S=0, V=0 (full black)
3. **Stroke width:** 1.5 mm
4. **Stroke style:** Solid Line
5. **Cap style:** Flat (to make spacing more noticeable)

### 2018 Lava Composite (Raster)

1. Turn on lava_composite_b10 raster layer
2. In Layer Styling, change **Symbology** from "Singleband gray" to "Singleband pseudocolor"
3. **Color ramp:** Select All color ramps → Yellow-Orange-Red (YlOrRd)
4. **Invert Color Ramp** (so intense red = high values)
5. Adjust **Min/Max values** to narrow the displayed range:
   - Min: 130,000
   - Max: 205,092
6. Check **Clip out of range values** to hide pixels outside this range
7. Click **Apply**

*[See the [original workshop](https://sites.google.com/stanford.edu/gis-cartography/workshops/maps-for-academic-journals) for step-by-step screenshots of raster styling.]*

### Place Names and Labels

1. Turn on Pop_Places layer
2. In Layer Styling, select **Simple Marker** → dot black symbol
3. Select the **Labels** option → change to "Single Labels"
4. **Value:** name field
5. **Text size:** 14 points, **Bold**
6. **Placement:** Cartographic → 4 mm offset

---

## Step 4: Open Print Composer

1. **Project → Layouts → Nature Specs**
2. Use the **Select/Move** tool (top of toolbar)
3. From the toolbar, select **Add new map** tool
4. Click and drag on the page to create a map frame

*[See the [original workshop](https://sites.google.com/stanford.edu/gis-cartography/workshops/maps-for-academic-journals) for detailed screenshots of the Print Composer interface and toolbar.]*

### Positioning and Scale

1. Use Select/Move to adjust the map frame
2. Or use **Position and Size** in the Item Properties panel
3. To set a specific scale, find **Main Properties** → enter scale (e.g., 70000 for 1:70,000)
4. Use the **Move Item Content** tool to pan/zoom data inside the frame
5. Adjust until your map data is centered and fills the layout

### Set Map Reference Scale (Optional)

If symbols appear too small/large in the print composer compared to the main view:

1. Return to main QGIS window
2. Right-click Pop_Places → **Properties → Rendering**
3. Check **Fixed Reference Scale** → select scale from Print Composer
4. In Print Composer, click **Update Map Preview**

---

## Step 5: Add Map Elements

### Add Title Text

1. Select **Add Label** tool
2. Click above the lava region to place a text box
3. In Item Properties, replace text with "East Rift Zone"
4. **Font:** Bold, 16 points
5. Use Select/Move to position it

*[See the [original workshop](https://sites.google.com/stanford.edu/gis-cartography/workshops/maps-for-academic-journals) for screenshots showing proper text placement and sizing.]*

### Add Legend

1. Select **Add Legend** tool
2. Click in the upper right corner of your layout
3. In Legend Items, **uncheck Auto Update**
4. Remove unwanted layers using the red minus (−) button
5. Keep only: lava composite raster, 1790/1840/1955 contacts
6. **Edit layer names:**
   - "lava_composite_b10" → "New Flow"
   - "1955_contacts" → "1955 Contacts" (repeat for others)
7. **Symbol properties:** Uncheck "Draw stroke for raster symbols"
8. **Remove frame** if desired or leave and modify
9. Position in the far right corner, using arrow keys to nudge it 5 steps down and left

*[See the [original workshop](https://sites.google.com/stanford.edu/gis-cartography/workshops/maps-for-academic-journals) for screenshots of the completed legend in context.]*

### Add Scale Bar

1. Select **Add Scale Bar** tool
2. Click in the lower right corner
3. In Item Properties:
   - **Style:** Line Ticks Up
   - **Units:** Kilometers
   - **Segments:** Fixed width 1 unit with 2 right segments
4. Adjust position to match legend spacing

### Add Locator Map with Extent Indicator (Optional)

1. Select **Map 1** (your main map) in the layout
2. In Layer Properties, check **Lock Layers** and **Lock styles for layers**
3. In main QGIS, duplicate coast_UTM5 layer
4. Keep only the new "coast_UTM5 copy" visible
5. Style it: full black fill, no outline
6. In Print Composer, use **Add Map** to create a new map in the upper left
7. In Item Properties:
   - **Background:** Set opacity to 50%
   - **Overviews:** + Add new → Set Map frame to "Map 1" → Frame style = red fill

### Add Image Background (Optional)

For added polish, add a paper texture:

1. Select **Add Picture** tool
2. Drag a box around the entire layout
3. In Item Properties, set file type to "Raster Image"
4. Browse to grunge_paper.jpg (from data folder images)
5. **Rendering:** Blending mode = Multiply, Opacity = 80%

---

## Step 6: Alignment and Spacing

1. **View** menu → Check **Show Grid** and **Snap to Grid**
2. Use **Smart Guides** to help align items
3. Ensure:
   - Even spacing between legend/scale bar and map edges
   - All elements aligned with each other
   - No visual clutter or overlapping text

*[See the [original workshop](https://sites.google.com/stanford.edu/gis-cartography/workshops/maps-for-academic-journals) for visual examples of well-aligned vs. poorly-aligned layouts.]*

---

## Step 7: Export the Final Map

1. In Print Layout, click **Export as Image** icon
2. Choose export folder and filename
3. **Image Export Options:**
   - **DPI:** 300 (or 600 for detailed maps)
   - **Color space:** RGB (publisher can convert to CMYK)
   - **Format:** .jpg (or per journal specs)
4. Click **Save**

---

## Extra: Create a Small Multiple

A **small multiple** shows the same map background with changing overlay data—useful for showing temporal changes or comparing data layers.

1. Create a new layout (4" × 16" for stacked maps)
2. Add a 3.5" × 3.5" map frame on the left
3. Style with only island background + 1790 contacts
4. Lock layers and lock styles
5. Copy/paste the map frame 3 more times, stacking vertically
6. For each subsequent map, unlock layers, add the next contact layer (1840, 1955), then add the 2018 raster
7. Use **Align and Distribute** tools to space evenly
8. Use arrow keys to fine-tune spacing

*[See the [original workshop](https://sites.google.com/stanford.edu/gis-cartography/workshops/maps-for-academic-journals) for a visual example of the completed small multiples layout.]*

---

# Additional Resources

For more on map design and GIS cartography:
- [Making Maps: A Visual Guide to Map Design for GIS (Krygier & Wood)](https://www.press.uchicago.edu/ucp/books/book/chicago/M/bo3637652.html)
- [Tufte, Edward. Envisioning Information](https://www.edwardtufte.com/tufte/et_books)
- [NASA Earth Observatory: Subtleties of Color](https://earthobservatory.nasa.gov/blogs/elegantfigures/2013/08/05/subtleties-of-color-part-1-of-6/)
- [Eager Eyes: Why Rainbow Colormaps are Problematic](https://eagereyes.org/basics/rainbow-color-map)
- [Natural Earth Data](https://www.naturalearthdata.com/) – Free geographic data
- [QGIS Documentation](https://docs.qgis.org/) – Official QGIS tutorials and guides

## What's Next?

With foundational cartographic design skills in place, you're ready to explore:
- Advanced vector analysis ([Week 03: Vector Data Analysis](../week03/))
- Raster data and remote sensing ([Week 05: Introduction to Raster Data](../week05/))
- Web mapping and interactive visualization ([Week 07: Publishing Web Maps](../week07/))

---

*Developed in cooperation with Dr. Michelle Tobias, GIS Data Specialist at UC Davis DataLab.*
