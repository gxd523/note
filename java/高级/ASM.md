字节码操纵框架

## ClassVisitor

### 执行顺序
visit visitSource? visitOuterClass? ( visitAnnotation | visitAttribute )*
( visitInnerClass | visitField | visitMethod )*
visitEnd

## MethodVisitor

### 执行顺序
visitAnnotationDefault?
( visitAnnotation | visitParameterAnnotation | visitAttribute )*
( visitCode
( visitTryCatchBlock | visitLabel | visitFrame | visitXxxInsn |
visitLocalVariable | visitLineNumber )*
visitMaxs )?
visitEnd

## ClassWriter

## 调试
* 在`Run/Debug Config`中点左上角`+`，选`Remote`，输入名称后确定
* 命令行输入`./gradlew clean :app:assembleDebug -Dorg.gradle.debug=true --no-daemon`
* 在`Run/Debug Config`选择刚才创建的`Remote`，点debug图标，开始调试