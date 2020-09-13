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