

A object which provides drawing state information to the General/NSOpenGLContext class. By providing a null-terminated array of pixel format attributes, you can set or toggle features such as colour depth, depth sorting, and double buffering -- features which require the cooperation of the operating system in addition to any related settings for the General/OpenGL state machine.

Example:

 General/NSOpenGLPixelFormatAttribute attr[] = 
 {
     General/NSOpenGLPFADoubleBuffer,
     General/NSOpenGLPFAAccelerated,
     General/NSOpenGLPFAColorSize, 32,
     General/NSOpenGLPFADepthSize, 16,
     0 };
 General/NSOpenGLPixelFormat *format = General/[[NSOpenGLPixelFormat alloc] initWithAttributes:attr];
 glContext = General/[[NSOpenGLContext alloc] initWithFormat: format shareContext: nil];
 [glContext makeCurrentContext];

--General/BrentGulanowski