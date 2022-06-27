# 파일 업로드
nest.js에서 파일 업로드는 `multer` 라는 express 미들웨어 라이브러리를 사용합니다. multer는 `multipart/form-data` 타입의 업로드만 지원한다고 합니다.

multer를 이용해 upload를 구현해 봅니다.

## multer 적용
multer의 타입 패키지를 설치합니다.
`$> npm i -D @types/multer`

다음 컨트롤러에서 다음과 같은 코드를 작성할 수 있습니다.
```typescript
// 파일
@Post('upload')
@UseInterceptors(FileInterceptor('file'))
getHello(@UploadedFile() file: Express.Multer.File) {
  console.log(file);
}

// 파일들
@Post('uploads')
@UseInterceptors(FilesInterceptor('files'))
uploadFile(@UploadedFiles() files: Array<Express.Multer.File>) {
  console.log(files);
}
```
FileInterceptor 인터셉터는 인자를 두개 받을 수 있는데 첫번째 인자는 `multipart/form-data` 에서 name을 의미합니다. 파일 혹은 파일들을 전송하기 때문에 위와 같이 설정합니다.
두번째 인자는 옵션이며 MulterOptions 객체 타입으로 받습니다.

MulterOptions 객체는 파일을 어디에 저장할지, 파일명은 어떻게 지정할지 등을 정의할 수 있습니다.
저는 최종적으로 업로드 처리를 위한 코드를 다음과 같이 작성하였습니다.

```typescript
   @Post()
   @UseInterceptors(FileInterceptor('file', {
     fileFilter: (req, file, cb) => {
       if (file.mimetype.match(/\/(jpg|png|jpeg|gif|svg|ico)$/i)) {
         cb(null, true);
       } else {
         cb(new BadRequestException('지원하지 않는 확장자입니다.'), false);
       }
     },
     storage: diskStorage({
       destination: (request, file, callback) => {
         const path = 'upload-file';
         if (!existsSync(path)) {
           mkdirSync(path);
         }
         callback(null, path);
       },
       filename(req, file, cb) {
         const chop = file.originalname.split('.').reverse();
         cb(null, `${randomUUID()}.${chop[0]}`);
       },
     }),
   }))
   async upload(@UploadedFile() file: Express.Multer.File): Promise<string> {
     if (file === undefined) {
       throw new BadRequestException('잘못된 업로드 방법입니다.');
     }
     return file.filename;
   }
```

파일의 확장자를 이용해 업로드 할 확장자를 필터링하며 업로드 할 경로는 지정합니다. 그리고 업로드 파일명은 임의의 UUID를 생성하여 명명합니다.
파일 업로드가 성공하면 파일명을 클라이언트에게 리턴합니다.

## 파일 다운로드
[링크](https://docs.nestjs.com/techniques/streaming-files) 를 이용하여 구현하였습니다.
