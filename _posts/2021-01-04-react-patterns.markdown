---
layout: post
title:  React Patterns
date:   2021-01-04 08:51:00 -0600
---

To wrap up my process of redux, I wanted to write a blogpost about a front end pattern from one of my previous projects that works well with redux. 
This post will go into how to break out code into separate files that will consolidate state, dispatching actions, and making API calls. For a deeper dive take
a look at the code [here](https://github.com/avni510/react-website)

For each box, I created a folder in my project. The box outlines the purpose of these files. Below I include a code example. 

![react_services](/assets/react-services.png)

```javascript
export type PhotoService = {
  loadPhoto(): Promise<string>
}

export class PhotoServiceAPI implements PhotoService {
  public async loadPhoto(): Promise<string> {
   return await func()
  }
}
const func = async () => "A photo"
```

&nbsp;
&nbsp;

![react_commands](/assets/react-commands.png)

```javascript
export interface PhotoCommands {
  fetchPhoto(): Promise<void>;
}

export class PhotoCommandsDispatcher implements PhotoCommands {
  constructor(
    private dispatch: Dispatch<PhotoActions>,
    private photoService: PhotoService
  ) {}

  public async fetchPhoto(): Promise<void> {
    this.dispatch({ type: 'Photo.Loading' });
    const photo = await this.photoService.loadPhoto();
    this.dispatch({ type: 'Photo.Loaded', photo });
  }
}
```

&nbsp;
&nbsp;

![react_state](/assets/react-state.png)
```javascript
export type PhotoState = {
  isLoading: boolean,
  photo?: string
};

export const initialPhotoState: PhotoState = {
  isLoading: true,
  photo: undefined
};
```

&nbsp;
&nbsp;

![react_reducer](/assets/react-reducer.png)
```javascript
export type PhotoActions =
{ type: 'Photo.Loading' } |
{ type: 'Photo.Loaded', photo: string }

export const photoReducer = (action: PhotoActions, photosState: PhotoState) => {
  switch (action.type) {
    case 'Photo.Loaded':
      return {
        ...photosState,
        isLoading: false,
        photo: action.photo
      }
    default:
      return photosState
  }
}
```
&nbsp;
&nbsp;

I then consolidated all the setup for the store into a `Setup` file. The separation of these files gives each file a specific purpose without bloating
its responsibility. As our app becomes more complex, we have specific files we can go in and add things to.  
