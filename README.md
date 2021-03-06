# Shopify Checkout Scripts Manager

[![Build status](https://img.shields.io/travis/ohmybrew/SCSM/master.svg)](https://travis-ci.org/ohmybrew/SCSM)
[![Coverage Status](https://coveralls.io/repos/github/ohmybrew/SCSM/badge.svg?branch=master)](https://coveralls.io/github/ohmybrew/SCSM?branch=master)

A sipme helper manager to process Javascript-based changes to Shopify Plus checkout.

Written in TypeScript with exports to plain Javascript and Browser (through Browserify).

## Installation

### Node

`npm i --save scsm`

### Browser

See `dist/scsm(.min).js`

## Usage

Add a script to your `layouts/checkout.liquid` file.

### TypeScript (Node)

```javascript
# TS version...
# checkout.ts

import { JobManager, BaseJob, IJob, Step, Event } from 'scsm';

// Example basic hello class
class Hello extends BaseJob implements IJob {
  // Fire only on payment method page and shipping method page
  public steps = [Step.PaymentMethod, Step.ShippingMethod];

  // Fire only for DOMContentLoaded
  public events = [Event.DomLoaded];

  // Run your code...
  run(): void {
    // Since subsribing to multiple steps, you can use
    // `this.isStep(Step.[Step Here])` to confirm the current step
    alert('Hello!');
  }
}

// Add the job
JobManager.add(new Hello());

// Fire all jobs tired to DOMContentLoaded
JobManager.execute(Event.DomLoaded);

// Fire all jobs tired to page:change
JobManager.execute(Event.PageChange);

// Fire all jobs tired to page:load
JobManager.execute(Event.PageLoad);
```

### Javascript (Node)

```javascript
# JS version...
# checkout.js

const { JobManager, BaseJob, Step, Event } = require('scsm');

// Example basic hello class
class Hello extends BaseJob {
  constructor() {
    super();

    // Fire only on payment method page and shipping method page
    this.steps = [Step.PaymentMethod, Step.ShippingMethod];

    // Fire only for DOMContentLoaded
    this.events = [Event.DomLoaded];
  }

  // Run your code...
  run() {
    // Since subsribing to multiple steps, you can use
    // `this.isStep(Step.[Step Here])` to confirm the current step
    // It also accepts an array of steps
    alert('Hello!');
  }
}

// Add the job
JobManager.add(new Hello());

// Fire all jobs tired to DOMContentLoaded
JobManager.execute(Event.DomLoaded);

// Fire all jobs tired to page:change
JobManager.execute(Event.PageChange);

// Fire all jobs tired to page:load
JobManager.execute(Event.PageLoad);
```

### Javascript (Browser)

```html
<!-- layouts/checkout.liquid -->
<!-- save scsm.min.js to assets/ -->
{{ 'scsm.min.js' | asset_url | script_url }}

<script>
  // SCSM is mapped to window.SCSM

  function Hello(type) {
    // Fire only on payment method page and shipping method page
    this.steps = [SCSM.Step.PaymentMethod, SCSM.Step.ShippingMethod];

    // Fire only for DOMContentLoaded
    this.events = [SCSM.Event.DomLoad];
  }

  /**
   * Checks if the step is active.
   * @param step The step to check.
   */
  Hello.prototype.isStep(step) {
    if (step.constructor === Array) {
      return step.indexOf(SCSM.JobManager.currentStep) > -1;
    }

    return SCSM.JobManager.currentStep === step;
  }
  
  Hello.prototype.run = function() {
    // Since subsribing to multiple steps, you can use
    // `this.isStep(Step.[Step Here])` to confirm the current step
    // It also accepts an array of steps
    alert('Hello!');
  };

  // Add the job
  SCSM.JobManager.add(new Hello());

  // Fire all jobs tired to DOMContentLoaded
  SCSM.JobManager.execute(Event.DomLoaded);

  // Fire all jobs tired to page:change
  SCSM.JobManager.execute(Event.PageChange);

  // Fire all jobs tired to page:load
  SCSM.JobManager.execute(Event.PageLoad);
</script>
```

## Development

Code is written in TypeScript based in `src` directory.

`npm run build` will convert the TypeScript code to Javascript and place output into `lib` directory.

## Packaging

`npm prepare` will compile the TypeScript, run Babelify and Browserify.

This will output TypeScript compile to `lib` directory.
This will output minfied browser compatible Javascript to `dist` directory.

## Testing

`npm test` through Jest. See `src/__tests__`.

## LICENSE

Released under the MIT License.
