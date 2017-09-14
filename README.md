# Sample: Hot Module Replacement
> Angular-HMR : Hot Module Replacement (HMR) is a feature to inject updated modules into the active runtime. It's like LiveReload for every module during development. HMR is "opt-in", so you need to put some code at chosen points of your application. The dependencies are handled by the module system.

## Set Up
1. Install the @angularclass/hmr dependency

    `npm install --save-dev @angularclass/hmr`

2. Create a file `hmr.ts` at src/hmr.ts to define the configuration
```typescript
  import { NgModuleRef, ApplicationRef } from '@angular/core';
  import { createNewHosts } from '@angularclass/hmr';

export const hmrBootstrap = (module: any, bootstrap: () => Promise<NgModuleRef<any>>) => {
  let ngModule: NgModuleRef<any>;
  module.hot.accept();
  bootstrap().then(mod => ngModule = mod);
  module.hot.dispose(() => {
    const appRef: ApplicationRef = ngModule.injector.get(ApplicationRef);
    const elements = appRef.components.map(c => c.location.nativeElement);
    const makeVisible = createNewHosts(elements);
    ngModule.destroy();
    makeVisible();
  });
};
```
3. Update main.ts to use hmr.ts. Refer main.ts
```typescript
 import { hmrBootstrap } from './hmr';
 import { environment } from './environments/environment';

  if (environment.hmr) {
    if (module['hot']) {
      hmrBootstrap(module, bootstrap);
  } else {
    bootstrap();
  }
```
4. Create environment file called 'environment.hmr.ts' at src/environments
```typescript
export const environment = {
 production: false,
 hmr: true
};
```

In enviroment.prod.ts; set hmr to false
```typescript
export const environment = {
 production: true,
 hmr: false
}
 ```

5. Jump to angular-cli.json and update 

```typescript
  "environmentSource": "environments/environment.ts",
    "environments": {
      "dev": "environments/environment.ts",
      "hmr": "environments/environment.hmr.ts",
      "prod": "environments/environment.prod.ts"
    },
  ```
