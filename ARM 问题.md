# ARM 问题

~~~c
printf("Address\tContent\n");
char* s = "Hello World!";
char* p=(char*)malloc(sizeof(char)*16);
strcpy(*p,s);
for(char* q;q!='\0';)
~~~