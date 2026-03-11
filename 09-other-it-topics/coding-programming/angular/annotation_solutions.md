# Annotation Solutions for Angular

Let's go with Konva.js

---

## Options

When working with Angular in 2026, your options for image annotation range from lightweight "plug-and-play" libraries for UI feedback to robust canvas frameworks for complex, custom editing tools.

Here are the best paths forward based on your specific requirements:

### 1. The Lightweight Choice: marker.js

If you need a ready-made UI that allows users to draw arrows, text, and shapes for feedback or documentation, **marker.js** is the most popular choice. It is framework-agnostic but integrates seamlessly with Angular.

- **Best for:** Visual feedback, simple markup, and "redlining" images.

- **Key Feature:** It provides a "headless" version (marker.js 3) that lets you style the UI to match your Angular design system exactly.

- **Implementation:**

Bash

  ```
  npm install @markerjs/markerjs3
  ```

------

### 2. The Interactive Specialist: Annotorious

If your goal is **image labeling** (e.g., for AI training or tagging specific regions with metadata), Annotorious is the industry standard. It handles the "selection + popup" workflow very well.

- **Best for:** Machine learning datasets, person and object tagging, and academic research.
- **Angular Wrapper:** Use `@architrixs/ngx-annotation`, which provides a dedicated Angular component for the library.
- **Features:** Supports bounding boxes and polygons out of the box.

------

### 3. High-Performance Custom Tools: Konva.js (ng2-konva)

If you need to build a heavy-duty editor (such as a Canva-like tool or a complex medical imaging tool), you should use a canvas-based library. **ng2-konva** provides a declarative Angular way to handle the HTML5 Canvas.

- **Best for:** High performance (1000+ annotations), drag-and-drop, and custom drawing logic.

- **Angular Integration:** Uses a `ko-stage` and `ko-layer` structure that feels like standard Angular templates.

- **Installation:**

Bash

  ```
  npm install ng2-konva konva
  ```

------

### 4. Comparison Overview

| **Feature**      | **marker.js**        | **Annotorious**           | **Konva.js**          |
| ---------------- | -------------------- | ------------------------- | --------------------- |
| **Primary Goal** | User Feedback        | Object Tagging            | Custom Canvas App     |
| **Complexity**   | Low (Ready to use)   | Medium                    | High (Custom built)   |
| **Performance**  | Good                 | Great                     | Excellent (GPU Accel) |
| **Output**       | Data (JSON) or Image | Data (W3C Web Annotation) | Data (JSON/Base64)    |

------

### Implementation Example: marker.js in Angular

Here is a quick look at how you would wrap a basic annotation tool in an Angular component:

**Component Logic (`annotate.component.ts`):**

TypeScript

```
import { Component, ElementRef, ViewChild } from '@angular/core';
import { MarkerArea } from '@markerjs/markerjs3';

@Component({
  selector: 'app-image-annotator',
  template: `
    <div class="container">
      <img #targetImage src="assets/sample-ui.png" (click)="startAnnotating()" />
    </div>
  `
})
export class ImageAnnotatorComponent {
  @ViewChild('targetImage') imageRef!: ElementRef<HTMLImageElement>;

  startAnnotating() {
    const ma = new MarkerArea(this.imageRef.nativeElement);

    // Subscribe to the render event to save the result
    ma.addEventListener('render', (event) => {
      this.imageRef.nativeElement.src = event.dataUrl;
      this.saveToDatabase(event.state); // Save the JSON state for future editing
    });

    ma.show();
  }

  saveToDatabase(state: any) {
    console.log('Annotation state saved:', state);
  }
}
```

---

## Medical Use Case

For a medical use case involving anatomical figures, precision and the ability to "re-edit" annotations are critical. You need a solution that supports freehand drawing (for irregular lesions), text labels (for descriptions), and the ability to save the annotation coordinates separately from the image so the original PNG remains intact.

Given these requirements, **Konva.js** (via **ng2-konva**) is the most professional choice. It allows you to layer a transparent canvas over your anatomical PNG, giving doctors a "digital acetate" to draw on.

### The Architecture Overview

1. **The Stage:** The main container that matches the image dimensions.
2. **The Image Layer:** Displays the anatomical PNG as the background.
3. **The Annotation Layer:** A vector layer where doctors draw lines, circles, or text.
4. **Data Persistence:** Instead of saving a flat image, you save a JSON array of coordinates. This allows a doctor to open the record later and move a "lesion" marker if it was misplaced.

------

### Implementation Guide: Medical Annotator

First, install the necessary packages:

```
npm install konva ng2-konva
```

#### 1. The Component Logic (`annotator.component.ts`)

We will set up a basic "Freehand" drawing mode and a "Text Note" mode.

TypeScript

```
import { Component, ViewChild } from '@angular/core';
import { KonvaComponent } from 'ng2-konva';
import Konva from 'konva';

@Component({
  selector: 'app-medical-annotator',
  template: `
    <div class="toolbar">
      <button (click)="setMode('brush')">Pen Tool</button>
      <button (click)="addNote()">Add Text Note</button>
      <button (click)="exportData()">Save Diagnosis</button>
    </div>

    <ko-stage [config]="configStage">
      <ko-layer #baseLayer>
        <ko-image [config]="configAnatomy"></ko-image>
      </ko-layer>
      <ko-layer #drawLayer>
        </ko-layer>
    </ko-stage>
  `
})
export class MedicalAnnotatorComponent {
  public configStage = { width: 600, height: 800 };
  public configAnatomy = { image: null, width: 600, height: 800 };
  private isDrawing = false;

  constructor() {
    const image = new Image();
    image.src = 'assets/human-anatomy-posterior.png';
    image.onload = () => {
      this.configAnatomy.image = image;
    };
  }

  addNote() {
    // Logic to drop a draggable text box at the center
    const textNode = new Konva.Text({
      text: 'Describe lesion here...',
      x: 50,
      y: 50,
      draggable: true,
      fontSize: 18,
      fill: 'red'
    });
    // Add to drawLayer programmatically
  }

  exportData() {
    // This exports the coordinates/JSON, not just a flat PNG
    const json = this.baseLayer.getStage().toJSON();
    console.log('Saved Medical State:', json);
  }
}
```

------

### Why this works for Medical Use Cases:

- **Precision:** Doctors can zoom in on specific areas (such as a small exit wound) without losing resolution in their drawings, because Konva uses vector paths.
- **Separation of Concerns:** You can store the "Annotation JSON" in your database linked to a Patient ID. You never have to modify the original medical reference image.
- **Layering:** You can include a "Skeletal Layer," a "Muscular Layer," and an "Annotation Layer," which the doctor can toggle on and off.

### Important Technical Considerations

1. **Scaling:** Ensure your canvas scales correctly if the window is resized; otherwise, the annotations will "drift" away from the anatomical landmarks.

2. **Pressure Sensitivity:** If doctors use tablets (such as an iPad Pro or Surface), Konva handles pointer events, enabling more natural lesion drawing.

---

## Saving Data

To store medical annotations in MySQL and re-render them accurately, you should treat the drawings as **structured data (JSON)** rather than a flat image file. This allows you to edit individual strokes or labels later without degrading the quality of the underlying anatomical PNG.

------

## 1. Data Structure & Storage Strategy

In MySQL, you should store the annotation "State" in a **JSON** column (available in MySQL 5.7+). This allows you to store complex coordinate arrays while keeping the database searchable.

### Database Schema Example

SQL

```
CREATE TABLE patient_annotations (
    id INT AUTO_INCREMENT PRIMARY KEY,
    patient_id INT NOT NULL,
    anatomy_type ENUM('posterior', 'anterior', 'skeletal') NOT NULL,
    -- Store the Konva/Canvas JSON here
    annotation_data JSON NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### What the JSON looks like

When a doctor draws a lesion, Konva generates a structure like this:

JSON

```
{
  "attrs": {"width": 600, "height": 800},
  "className": "Stage",
  "children": [
    {
      "className": "Layer",
      "children": [
        {
          "className": "Line",
          "attrs": {
            "points": [10, 20, 15, 25, 30, 40],
            "stroke": "red",
            "strokeWidth": 5,
            "name": "lesion-area"
          }
        }
      ]
    }
  ]
}
```

------

## 2. The Extraction and Re-rendering Flow

To re-render the drawings, the Angular application follows a "Hydration" process:

1. **Fetch:** Angular calls your API (`GET /annotations/{id}`).
2. **Load Image:** The background anatomical PNG is loaded into the canvas first.
3. **Import JSON:** The `annotation_data` string from MySQL is passed into the Konva stage.
4. **Sync:** The canvas redraws the vectors exactly where the doctor left them.

------

## 3. Implementation: Saving and Loading

Here is how you handle the "Save" and "Load" logic in your Angular component.

### Saving to MySQL (via API)

TypeScript

```
saveAnnotation() {
  // 1. Get the drawing layer as a JSON string
  const stageJson = this.stage.getStage().toJSON();

  // 2. Send to your Node/C#/Java backend to save in MySQL
  this.http.post('/api/annotations', {
    patientId: 123,
    data: stageJson
  }).subscribe(() => console.log('Stored in MySQL successfully'));
}
```

### Re-rendering from MySQL

TypeScript

```
loadAnnotation(annotationId: number) {
  this.http.get<any>(`/api/annotations/${annotationId}`).subscribe(res => {
    // 1. Clear the current stage
    this.stage.getStage().destroyChildren();

    // 2. Re-create the stage from the stored JSON
    const savedStage = Konva.Node.create(res.annotation_data, 'container-id');

    // 3. Add back to your Angular component view
    this.stage.getStage().add(savedStage);
  });
}
```

------

## 4. Adding Undo/Redo Functionality

In a medical context, mistakes happen. To implement Undo, you maintain a "History" stack of your JSON states.

TypeScript

```
private history: string[] = [];
private step = -1;

// Call this every time a doctor finishes a stroke
saveHistory() {
  const state = this.stage.getStage().toJSON();
  this.history.push(state);
  this.step++;
}

undo() {
  if (this.step > 0) {
    this.step--;
    const previousState = this.history[this.step];
    // Re-render the stage using Konva.Node.create()
  }
}
```

### Summary of Benefits

- **Non-destructive:** The original anatomical PNG is never altered.
- **Scalable:** You can search MySQL for all patients with "red" annotations or specific text notes.
- **Responsive:** Because it's vector data, the notes will look sharp on a 4K monitor or a tablet.

