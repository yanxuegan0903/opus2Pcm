# opus2Pcm
一个opus转Pcm 的库



使用示例

#define PCM_BUFFER_SIZE 480
static BOOL audio_inited = NO;
static opus_uint32 channels = 1;
static opus_int32 sampling_rate = 8000;
static OpusDecoder *dec = NULL;
static short out_opus_pcm[PCM_BUFFER_SIZE] = {0};//opus专用

-(BOOL)initOpus
{
    if (audio_inited) return YES;
    //初始化Opus解码库
    int err;
    dec = opus_decoder_create(sampling_rate, channels, &err);
    if (err != OPUS_OK)
    {
        fprintf(stderr, "Cannot create encoder: %s\n", opus_strerror(err));
        dec = NULL;
        return FALSE;
    }
    
    audio_inited = YES;
    return YES;
}

-(int)OpusDecode:(NSData*)data{
    [self initOpus];//内部做了判断不会多次调用
    //opus解码
    memset(out_opus_pcm, 0, 2 * PCM_BUFFER_SIZE);
    int output_samples = opus_decode(dec, data.bytes, (int)data.length, out_opus_pcm, PCM_BUFFER_SIZE, 0);
    return output_samples;
}
