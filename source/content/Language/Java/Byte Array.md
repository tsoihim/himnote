### 개요
Java에서는 원시 타입으로 `byte`를 지원
- byte의 범위는 -128부터 127까지
- 아래와 같은 코드를 실행하면 결과값이 72인 것을 확인 가능
	- `System.out.println("Hello".getBytes()[0]);`
- 원시 타입이므로 당연히 array로 사용할 수 있으며, 많은 객체들이 객체를 byte array 형태로 표현해주는 getBytes 등의 메소드를 지원
	- 네트워킹, 암호화 등 byte 데이터를 직접 처리해야하는 경우 등 유용하게 사용됨

### ByteArrayOutputStream
byte array는 기본적으로 고정 길이를 가지는 원시타입의 배열 형태이므로, 여러 배열을 붙이고 자르는 등의 작업을 수행하기에는 번거로움

ByteArrayOutputStream은 java.io 패키지에 내장된 클래스로, 동적인 바이트 배열을 조작하는 경우 유용

기본적인 코드 예제는 아래와 같음
- 아래 코드를 실행하면 결과로 6을 확인할 수 있음
```Java
byte[] a = {1, 2, 3};
byte[] b = {1, 2, 3};

ByteArrayOutputStream stream = new ByteArrayOutputStream();

stream.write(a);
stream.write(b);

System.out.println(stream.toByteArray().length);
```

한편, 동적으로 길이를 조정할 수 있기 때문에 그만큼 성능 제약이 발생

아래는 ByteArrayOutputStream의 소스코드
- 실제 byte array를 나타내는 buf와 count 값을 가짐
```Java
public class ByteArrayOutputStream extends OutputStream {
    /**
     * The buffer where data is stored.
     */
    protected byte buf[];

    /**
     * The number of valid bytes in the buffer.
     */

    protected int count;
	...
	...    
}
```

ByteArrayOutputStream은 count 값을 이용해 동적인 바이트 배열 처리를 지원
write가 일어날 때마다 아래의 ensureCapacity를 이용하여 동적으로 배열 관리
- 배열 크기를 늘려야하는 경우 Copy가 반복적으로 발생하므로 성능상 불리
- toByteArray 호출 시에도 count에 맞는 buf 내 데이터만 리턴하기 위해 Copy 호출
```Java
    /**
     * Increases the capacity if necessary to ensure that it can hold
     * at least the number of elements specified by the minimum
     * capacity argument.
     *
     * @param  minCapacity the desired minimum capacity.
     * @throws OutOfMemoryError if {@code minCapacity < 0} and
     * {@code minCapacity - buf.length > 0}.  This is interpreted as a
     * request for the unsatisfiably large capacity.
     * {@code (long) Integer.MAX_VALUE + (minCapacity - Integer.MAX_VALUE)}.
     */
    private void ensureCapacity(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = buf.length;
        int minGrowth = minCapacity - oldCapacity;
        if (minGrowth > 0) {
            buf = Arrays.copyOf(buf, ArraysSupport.newLength(oldCapacity,
                    minGrowth, oldCapacity /* preferred growth */));
        }
    }

   /**
     * Writes the specified byte to this {@code ByteArrayOutputStream}.
     *
     * @param   b   the byte to be written.
     */
    public synchronized void write(int b) {
        ensureCapacity(count + 1);
        buf[count] = (byte) b;
        count += 1;
    }

   /**
     * Creates a newly allocated byte array. Its size is the current
     * size of this output stream and the valid contents of the buffer
     * have been copied into it.
     *
     * @return  the current contents of this output stream, as a byte array.
     * @see     java.io.ByteArrayOutputStream#size()
     */
    public synchronized byte[] toByteArray() {
        return Arrays.copyOf(buf, count);
    }
```

write시 무분별한 Copy 호출을 막고 싶은 경우, 기본 생성자 대신 아래 생성자를 이용하여 최소한의 예상되는 buf 크기를 잡아주는 것이 좋음
```Java
    /**
     * Creates a new {@code ByteArrayOutputStream}, with a buffer capacity of
     * the specified size, in bytes.
     *
     * @param  size   the initial size.
     * @throws IllegalArgumentException if size is negative.
     */
    public ByteArrayOutputStream(int size) {
        if (size < 0) {
            throw new IllegalArgumentException("Negative initial size: "
                                               + size);
        }
        buf = new byte[size];
    }
```