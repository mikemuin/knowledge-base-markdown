# Angular Forms Engine — Recommended Stack

## Guiding Principle

Angular is an excellent choice for this architecture because its **reactive forms system**, **dependency injection**, and **strong TypeScript typing** align naturally with a schema-driven, concept-bound forms engine. The goal is to select libraries that handle undifferentiated heavy lifting so your team focuses on clinical logic, not UI plumbing.

------

## The Complete Recommended Stack

### Core Angular Foundation

**Angular 17+** is the baseline. Specifically because of three features that directly benefit your architecture.

Standalone components eliminate NgModule boilerplate, meaning each widget in your widget registry is a self-contained, lazily loadable unit. Signal-based reactivity (introduced in Angular 16, stabilized in 17) is a better fit for a forms engine than traditional zone-based change detection because vital sign fields update frequently and independently — you do not want the entire form re-rendering when a single numeric field changes. The new control flow syntax (`@if`, `@for`, `@switch`) replaces `*ngIf` and `*ngFor` with block syntax that is more readable when building dynamic widget trees from JSON.

```typescript
// Widget registry entry — each widget is a standalone component
@Component({
  selector: 'app-number-input-widget',
  standalone: true,
  imports: [ReactiveFormsModule, CommonModule],
  template: `...`
})
export class NumberInputWidget implements WidgetComponent {
  @Input() question!: ResolvedQuestion;
  @Input() formGroup!: FormGroup;
}
```

------

### Schema Validation and Type Safety

**Zod** is the recommended library for runtime JSON schema validation on the form definition at ingestion time. It generates TypeScript types from schemas, meaning your form definition structure is validated both at upload time on the backend and at compile time in your Angular services.

```typescript
import { z } from 'zod';

const QuestionSchema = z.object({
  id: z.string(),
  type: z.enum([
    'number', 'select', 'datetime', 'textarea',
    'ui-select-extended', 'obsGroup'
  ]),
  label: z.string(),
  concept: z.string().regex(/^[A-Z]+:\d+$/),
  required: z.boolean().default(false),
  unit: z.string().optional(),
  referenceRangeKey: z.string().optional(),
  alertRuleKey: z.string().optional(),
  calculatedExpression: z.string().optional(),
  readonly: z.boolean().optional(),
  trend: z.boolean().optional(),
  hide: z.object({
    hideWhenExpression: z.string()
  }).optional(),
  crossValidate: z.array(z.object({
    ruleId: z.string(),
    expression: z.string(),
    message: z.string()
  })).optional(),
  questions: z.array(z.lazy(() => QuestionSchema)).optional() // nested
});

export type Question = z.infer<typeof QuestionSchema>;
```

------

### Reactive Forms Engine

**Angular Reactive Forms** is the native choice and the right one. Do not use template-driven forms for a schema-driven engine — the programmatic control that `FormBuilder`, `FormGroup`, and `FormArray` give you is essential for building a form tree dynamically from JSON.

The key pattern is a **FormFactory service** that walks the resolved JSON and produces a corresponding `FormGroup` tree.

```typescript
@Injectable({ providedIn: 'root' })
export class FormFactoryService {

  constructor(private fb: FormBuilder) {}

  buildFormGroup(questions: ResolvedQuestion[]): FormGroup {
    const controls: Record<string, AbstractControl> = {};

    for (const question of questions) {
      if (question.type === 'obsGroup' && question.repeating) {
        controls[question.id] = this.fb.array(
          [], this.buildGroupValidators(question)
        );
      } else if (question.type === 'obsGroup') {
        controls[question.id] = this.buildFormGroup(question.questions ?? []);
      } else {
        controls[question.id] = this.fb.control(
          { value: question.defaultValue ?? null,
            disabled: question.readonly ?? false },
          this.buildValidators(question)
        );
      }
    }
    return this.fb.group(controls);
  }

  private buildValidators(q: ResolvedQuestion): ValidatorFn[] {
    const validators: ValidatorFn[] = [];
    if (q.required) validators.push(Validators.required);
    if (q.validators) {
      for (const v of q.validators) {
        if (v.type === 'range') {
          validators.push(Validators.min(v.min));
          validators.push(Validators.max(v.max));
        }
      }
    }
    return validators;
  }
}
```

------

### Dynamic Widget Rendering

**Angular CDK — ComponentPortal** is the correct mechanism for rendering widgets dynamically from the registry without a giant `ngSwitch`. Each widget type in the registry maps to a component class, and the renderer instantiates whichever component the `type` field specifies.

```typescript
// Widget registry service
@Injectable({ providedIn: 'root' })
export class WidgetRegistryService {

  private registry = new Map<string, Type<WidgetComponent>>([
    ['number',              NumberInputWidget],
    ['select',              ConceptSelectWidget],
    ['datetime',            DateTimeWidget],
    ['textarea',            FreeTextWidget],
    ['ui-select-extended',  AsyncSearchSelectWidget],
    ['obsGroup',            ObsGroupWidget],
  ]);

  resolve(type: string): Type<WidgetComponent> {
    const widget = this.registry.get(type);
    if (!widget) throw new Error(`Unknown widget type: ${type}`);
    return widget;
  }
}

// Dynamic widget host directive
@Directive({ selector: '[widgetHost]', standalone: true })
export class WidgetHostDirective {
  constructor(public viewContainerRef: ViewContainerRef) {}
}

// Widget renderer component
@Component({
  selector: 'app-widget-renderer',
  standalone: true,
  imports: [WidgetHostDirective],
  template: `<ng-container widgetHost></ng-container>`
})
export class WidgetRendererComponent implements OnInit {
  @Input() question!: ResolvedQuestion;
  @Input() formGroup!: FormGroup;
  @ViewChild(WidgetHostDirective, { static: true })
    widgetHost!: WidgetHostDirective;

  constructor(private registry: WidgetRegistryService) {}

  ngOnInit(): void {
    const component = this.registry.resolve(this.question.type);
    const ref = this.widgetHost.viewContainerRef
      .createComponent(component);
    ref.instance.question = this.question;
    ref.instance.formGroup = this.formGroup;
  }
}
```

------

### Skip Logic and Expression Evaluation

**expr-eval** is the recommended library for evaluating the skip logic expressions like `hideWhenExpression: "isEmpty(q_respiratory_rate)"`. It is a sandboxed mathematical and logical expression evaluator that does not use `eval()`, making it safe for clinical environments where XSS risk must be contained.

```typescript
import { Parser } from 'expr-eval';

@Injectable({ providedIn: 'root' })
export class SkipLogicService {

  private parser = new Parser();

  constructor() {
    // Register custom clinical functions
    this.parser.functions.isEmpty = (val: unknown) =>
      val === null || val === undefined || val === '';
    this.parser.functions.computeTriageLevel =
      (hr: number, rr: number, temp: number,
       sbp: number, dbp: number, spo2: number) =>
        this.triageCompute(hr, rr, temp, sbp, dbp, spo2);
  }

  evaluate(expression: string, context: Record<string, unknown>): unknown {
    try {
      return this.parser.evaluate(expression, context);
    } catch {
      return false; // fail safe — show the field if expression errors
    }
  }

  shouldHide(question: ResolvedQuestion,
             formValues: Record<string, unknown>): boolean {
    if (!question.hide?.hideWhenExpression) return false;
    return !!this.evaluate(
      question.hide.hideWhenExpression, formValues
    );
  }

  private triageCompute(hr: number, rr: number, temp: number,
                         sbp: number, dbp: number, spo2: number): string {
    const critical = hr < 40 || hr > 150 || rr < 8 || rr > 30
      || temp < 35.0 || temp >= 41.0 || sbp < 70
      || sbp >= 180 || spo2 < 90;
    const emergent = !critical && (hr < 50 || hr > 130
      || rr < 10 || rr > 25 || sbp < 80 || sbp >= 160
      || spo2 < 93);
    if (critical) return 'CIEL:162644';
    if (emergent) return 'CIEL:162645';
    return 'CIEL:162647';
  }
}
```

------

### UI Component Library

**Angular Material (MDC-based)** is the baseline recommendation for the widget implementations themselves. It provides the input, select, datepicker, and autocomplete primitives your widgets need. It is accessibility-compliant out of the box, which matters for clinical environments, and its theming system supports facility-level branding.

For the clinical-specific display elements — reference range indicators, trend sparklines, alert banners — you will build custom components on top of Angular Material primitives rather than relying on a third-party component library for those, since clinical UX requirements are specific enough that off-the-shelf components rarely fit without heavy modification.

**ng2-charts (Chart.js wrapper)** is the recommendation for trend sparklines. It is lightweight, supports real-time streaming updates via `data$` observable binding, and the Chart.js canvas renderer performs well on low-end tablet hardware common in clinical settings.

```typescript
// Trend sparkline widget
@Component({
  selector: 'app-trend-sparkline',
  standalone: true,
  imports: [NgChartsModule],
  template: `
    <canvas baseChart
      [data]="chartData"
      [type]="'line'"
      [options]="sparklineOptions">
    </canvas>
  `
})
export class TrendSparklineComponent {
  @Input() set observations(obs: HistoricalObs[]) {
    this.chartData = {
      labels: obs.map(o => o.date),
      datasets: [{
        data: obs.map(o => o.value),
        borderColor: this.getRangeColor(obs),
        tension: 0.3,
        pointRadius: 2
      }]
    };
  }

  chartData: ChartData<'line'> = { labels: [], datasets: [] };

  sparklineOptions: ChartOptions<'line'> = {
    plugins: { legend: { display: false } },
    scales: { x: { display: false }, y: { display: false } },
    responsive: true,
    maintainAspectRatio: false
  };
}
```

------

### State Management

**NgRx SignalStore** (introduced in NgRx 17) is the recommended state management approach. It is lighter than the traditional NgRx store, integrates natively with Angular signals, and the slice-per-concern pattern maps cleanly onto your forms engine domains.

```typescript
// Form engine state
export const FormEngineStore = signalStore(
  { providedIn: 'root' },
  withState({
    resolvedSchema: null as ResolvedFormSchema | null,
    formValues: {} as Record<string, unknown>,
    visibilityMap: {} as Record<string, boolean>,
    alertMap: {} as Record<string, ClinicalAlert[]>,
    dirtyFields: new Set<string>(),
    submitting: false,
    submitError: null as string | null
  }),
  withComputed((store) => ({
    activeAlerts: computed(() =>
      Object.values(store.alertMap())
        .flat()
        .filter(a => a.severity === 'CRITICAL')
    ),
    formIsValid: computed(() =>
      store.resolvedSchema() !== null &&
      Object.keys(store.formValues()).length > 0
    )
  })),
  withMethods((store) => ({
    updateFieldValue(fieldId: string, value: unknown): void {
      patchState(store, (s) => ({
        formValues: { ...s.formValues, [fieldId]: value },
        dirtyFields: new Set([...s.dirtyFields, fieldId])
      }));
    }
  }))
);
```

------

### HTTP and Offline Support

**Angular HttpClient with interceptors** handles the API layer — form schema resolution, concept lookups, observation submission. Two interceptors are essential.

The **auth interceptor** injects the JWT and the `app.current_user_id` header your PostgreSQL RLS policies depend on. The **offline interceptor** queues failed POST requests to IndexedDB when network is unavailable and replays them when connectivity is restored.

**Dexie.js** is the recommended IndexedDB wrapper for offline storage. It is Promise and Observable-friendly, supports complex queries, and its schema migration system is mature enough to handle form schema version changes gracefully.

```typescript
@Injectable()
export class OfflineQueueInterceptor implements HttpInterceptor {

  constructor(private offlineQueue: OfflineQueueService) {}

  intercept(req: HttpRequest<unknown>,
            next: HttpHandler): Observable<HttpEvent<unknown>> {
    if (req.method !== 'POST') return next.handle(req);

    return next.handle(req).pipe(
      catchError((error: HttpErrorResponse) => {
        if (!navigator.onLine) {
          return from(
            this.offlineQueue.enqueue(req)
          ).pipe(
            map(() => new HttpResponse({
              status: 202,
              body: { queued: true, localId: crypto.randomUUID() }
            }))
          );
        }
        return throwError(() => error);
      })
    );
  }
}
```

------

### Form Builder UI (For Your IT Team)

The authoring UI where devs upload and manage form definitions should be built with **Angular JSON Forms** as a starting point or reference. It provides a Monaco Editor integration for raw JSON editing with schema-aware autocomplete, a visual tree editor for non-technical clinical informatics staff, and a live preview pane that runs the actual widget renderer against the form definition being authored.

**Monaco Editor (via ngx-monaco-editor)** is the recommended JSON editing component. It provides JSON schema validation with inline error highlighting, autocomplete against your widget registry schema, and formatting — which significantly reduces dev errors during form definition authoring.

------

## Complete Stack Summary

| Concern               | Library / Approach                        |
| --------------------- | ----------------------------------------- |
| Core framework        | Angular 17+ standalone + signals          |
| Type safety           | TypeScript strict + Zod schema validation |
| Forms engine          | Angular Reactive Forms + FormBuilder      |
| Dynamic widgets       | Angular CDK ComponentPortal               |
| Expression evaluation | expr-eval (sandboxed, no eval)            |
| UI components         | Angular Material MDC                      |
| Charts / Sparklines   | ng2-charts (Chart.js)                     |
| State management      | NgRx SignalStore                          |
| HTTP + Auth           | Angular HttpClient + interceptors         |
| Offline storage       | Dexie.js (IndexedDB)                      |
| Form authoring UI     | Monaco Editor via ngx-monaco-editor       |
| Testing               | Jest + Angular Testing Library + Cypress  |

------

## The Architecture in One Diagram

```
┌─────────────────────────────────────────────────────────┐
│                  Angular Application                     │
│                                                          │
│  ┌──────────────┐    ┌──────────────────────────────┐   │
│  │  Form Schema │    │     NgRx SignalStore          │   │
│  │  Service     │───►│  formValues / alertMap /      │   │
│  │  (HTTP)      │    │  visibilityMap / dirtyFields  │   │
│  └──────────────┘    └──────────────┬───────────────┘   │
│                                     │                    │
│  ┌──────────────┐    ┌──────────────▼───────────────┐   │
│  │  FormFactory │───►│     Widget Tree               │   │
│  │  Service     │    │  (ComponentPortal renderer)   │   │
│  │  (Zod typed) │    │                               │   │
│  └──────────────┘    │  Section > Question > Widget  │   │
│                      │  NumberInput | Select |        │   │
│  ┌──────────────┐    │  DateTime | ObsGroup | ...    │   │
│  │  SkipLogic   │───►│                               │   │
│  │  Service     │    │  Each widget is standalone    │   │
│  │  (expr-eval) │    │  Angular component from       │   │
│  └──────────────┘    │  WidgetRegistry               │   │
│                      └──────────────┬───────────────┘   │
│  ┌──────────────┐                   │                    │
│  │  Offline     │    ┌──────────────▼───────────────┐   │
│  │  Queue       │    │     Submission Service        │   │
│  │  (Dexie.js)  │◄───│  Serialize → POST obs bundle │   │
│  └──────────────┘    └──────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

The ComponentPortal + WidgetRegistry pattern is the architectural centerpiece. It means your IT team can add a brand new widget type — say a `signature-pad` widget for consent forms or a `barcode-scanner` widget for specimen tracking — by registering one new standalone component in the registry and adding its type string to the Zod schema enum. No other part of the forms engine changes.